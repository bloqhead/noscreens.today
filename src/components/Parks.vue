<template>
  <v-layout row wrap>
    <v-flex xs12>

      <v-list two-line>
        <template v-for="( item, index ) in displayedItems">
          <v-list-tile :key="item.name">

            <v-list-tile-avatar>
              <v-btn :href="item.url" target="_blank" fab small color="teal teal-lighten-2">
                <v-icon medium dark>link</v-icon>
              </v-btn>
            </v-list-tile-avatar>

            <v-list-tile-content>
              <v-list-tile-title>{{ item.name }}</v-list-tile-title>
              <v-list-tile-sub-title>{{ item.address }}</v-list-tile-sub-title>
              <v-list-tile-sub-title>{{ item.subtitle }}</v-list-tile-sub-title>
            </v-list-tile-content>

            <v-list-tile-action>
              <v-list-tile-action-text>
                <strong>{{ roundUp(item.acreage, 1) }} acres</strong>
              </v-list-tile-action-text>
              <v-list-tile-action-text>

                <v-chip
                  v-if="item.access === 'fee'"
                  color="red"
                  text-color="white"
                  label
                  small>
                  entry fee
                </v-chip>

                <v-chip
                  v-else
                  color="teal"
                  text-color="black"
                  label
                  small>
                  free entry
                </v-chip>

              </v-list-tile-action-text>
            </v-list-tile-action>

          </v-list-tile>
          <v-divider :key="index" :inset="item.inset"></v-divider>
        </template>
      </v-list>

      <v-bottom-nav class="nav__bottom" :value="true" fixed app>
        <v-pagination
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
      dataSrc: './data.json',
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
          this.items = res.data
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
</style>
