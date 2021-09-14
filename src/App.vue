<template>
  <div id="app">
    <b-container>
      <numarete v-bind:height="200" v-bind:width="100"></numarete>
    </b-container>
  </div>
</template>

<script>
import Vue from 'vue'
import numarete from './components/numarete.vue'
import BootstrapVue  from 'bootstrap-vue'

import 'bootstrap/dist/css/bootstrap.css'
import 'bootstrap-vue/dist/bootstrap-vue.css'

Vue.use(BootstrapVue)

export default {
  name: 'App',
  components: {
    numarete
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
