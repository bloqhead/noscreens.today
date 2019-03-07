<template>
  <v-layout row wrap>
    <v-flex xs12>

      <v-list two-line>

        <div v-if="!items.length" class="text-xs-center pt-4 pb-4">
          <v-progress-circular
            :size="70"
            :width="6"
            color="teal teal-lighten-3"
            indeterminate
          ></v-progress-circular>
        </div>

        <template v-else v-for="( item, index ) in displayedItems">
          <v-list-tile :key="item.fullName" :id="'park-'+item.parkCode">

            <v-list-tile-avatar>
              <v-btn :href="item.url" target="_blank" fab small color="teal">
                <v-icon medium>link</v-icon>
              </v-btn>
            </v-list-tile-avatar>

            <v-list-tile-content>
              <v-list-tile-title>{{ item.fullName }}</v-list-tile-title>
              <v-list-tile-sub-title>{{ item.states }}</v-list-tile-sub-title>
            </v-list-tile-content>

            <v-list-tile-action>
              <v-list-tile-action-text>
                <v-btn :href="item.directionsUrl" target="_blank" color="teal" small>Directions</v-btn>
              </v-list-tile-action-text>
            </v-list-tile-action>

          </v-list-tile>
          <v-divider :key="index" :inset="item.inset"></v-divider>
        </template>
      </v-list>

      <v-bottom-nav v-if="items.length" class="nav__bottom" :value="true" fixed app>
        <v-pagination
          color="teal"
          v-model="page"
          :length="getNumberOfPages( items, perPage )"
          :total-visible="7"
        ></v-pagination>
      </v-bottom-nav>

    </v-flex>

  </v-layout>
</template>

<script>
import axios from 'axios'

export default {
  data() {
    return {
      dataSrc: `${process.env.VUE_APP_NPS_API_URL}?api_key=${process.env.VUE_APP_NPS_API_KEY}&limit=500&sort=states`,
      items: [],
      page: 1,
      perPage: 8,
      pages: []
    }
  },
  methods: {
    roundUp(num, precision) {
      precision = Math.pow(10, precision)
      let value = Math.ceil(num * precision) / precision
      return value.toLocaleString()
    },
    fetchData() {
      axios.get( this.dataSrc )
        .then( res => {
          this.items = res.data.data
        })
        .catch( error => {
          console.log(error)
        })
    },
    getNumberOfPages( items, perPage ) {
      return Math.ceil( items.length / perPage )
    },
    setPages() {
      let numberOfPages = this.getNumberOfPages( this.items, this.perPage )
      for ( let index = 1; index <= numberOfPages; index++ ) {
        this.pages.push(index)
      }
    },
    paginate( items ) {
      let page = this.page
      let perPage = this.perPage
      let from = ( page * perPage ) - perPage
      let to = ( page * perPage )
      let selectedItems = items.slice( from, to )
      return selectedItems
    }
  },
  created() {
    this.fetchData()
  },
  watch: {
    items() {
      this.setPages()
    }
  },
  computed: {
    displayedItems() {
      return this.paginate(this.items)
    }
  }
}
</script>

<style lang="scss">
.nav__bottom > * {
  justify-content: center;
}

.v-divider:nth-child(16n) {
  display: none;
}

.v-list__tile__action-text .v-btn {
  padding: 0 8px;
}
</style>
