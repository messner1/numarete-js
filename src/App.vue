<template>
  <div id="app" class=container-md>
    <b-row align-h = "center"> 
       <h2>The Numa-rete: A Biologically-Inspired Shape Recognition System</h2>
        <h3> A simulation by Craig Messner</h3>
    </b-row>
      <br>
    <b-row align-h = "center"> 
      <b-col cols="2"></b-col>
      <b-col cols="8">
        <p> The Numa-rete (or Numarete) was a project created in the Heinz Von Foerster-led Biological Computer Lab at the University of Illinois. Former BCL affiliate and University of Illinois professor Paul Weston <a href=https://www.univie.ac.at/constructivism/HvF/festschrift/weston.html>describes</a> the construction of the machine as: "consisting of an iterated network of identical elements, which could instantly report the number of areas of shadow, i.e., "objects," cast on its photocell "retina," utterly independently of their shape or position." The Numarete was an early experiment in biologically-inspired parallel computing, with Von Foerster himself calling it an "artificial retina." It employs connected layers of photosensitive nodes, Weston's "identical elements" in order to instantaneously detect the number of convex shapes placed on its similarly photosensitive surface, an feat no doubt impressive given the speed of more traditional digital computers in the 1960s and 70s. As he makes it clear in his paper "Perception of Form in Biological and Man-Made Systems," Von Foerster himself saw the machine as replicating the sort of cybernetic interplay between system and self that leads to gestalt notions of number (one-ness, two-ness, so forth and so on). The combination of convenient mathematical rules (in this case those governing convex shapes) and simple "biological" elements results in a complex calculation without the need for the computational power or time a more traditional iterative algorithm might demand. In Von Foerster's account, this interplay is what accounts for biology's striking success in areas computers  fear to tread.</p>

        <p> The below is a simulation of the Numarete. Clicking in the field begins and ends lines, allowing for the creation of arbitrary shapes. If the shape is not convex, the simulation will remove it. Adding a shape immediately calculates the number now contained in the field in a fashion (as best I can tell) identical to the original machine. Below the field are boxes indicating the state of each node in each layer, with a brief description of the layer's purpose and function. The counting task itself is redundant in this modern form. After all, the simulation already detects convex shapes in order to make sure you are creating one. However, I hope that this simulation captures at least some of the wonder Numarete no doubt provoked in the era of its creation. It, and many projects of the BCL, are unjustly forgotten.  Despite this, projects such as this one or the <a href=https://en.wikipedia.org/wiki/FERMIAC>FERMIAC</a> still exert subtle, unrecognized influence on approaches to computing in the modern era. They form chapters in an alternate history of computing, one not so dominated by the von Neumann architecture. Understanding them both denaturalizes the narrative that presents such machines as the inevitable "winners" of computer history and offers lessons for how to approach contemporary problems in computing from a different angle</p>
      </b-col>
      <b-col cols="2"></b-col>
    </b-row>

    <numarete v-bind:height="200" v-bind:width="100"></numarete>
  </div>
</template>

<script>
import Vue from 'vue'
import BootstrapVue from 'bootstrap-vue'
import numarete from './components/numarete.vue'

Vue.use(BootstrapVue)

import 'bootstrap/dist/css/bootstrap.css'
import 'bootstrap-vue/dist/bootstrap-vue.css'

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
