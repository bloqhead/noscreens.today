<template>
  <div id="app">
    <v-app dark>
      <v-layout align-center justify-center row fill-height>
        <v-flex xs12 m6>
          <Header/>
          <v-content>
            <v-container>
              <router-view></router-view>
            </v-container>
          </v-content>
        </v-flex>
      </v-layout>
    </v-app>
  </div>
</template>

<script>
import Header from '@/components/Header.vue'

export default {
  components: {
    Header
  },
  metaInfo: {
    title: 'noscreens.today',
    titleTemplate: '%s | Florida State Park Database',
    meta: [
      {
        name: 'description',
        content: 'noscreens.today is a free tool that provides you with a simple database of Florida state parks for weekend day escapes. No fluff. Just parks and their info.'
      }
    ]
  },
}
</script>
