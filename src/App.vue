<template>
  <div id="app">
    <img id='cat' src="./assets/logo.png">
    <HelloWorld msg="Welcome to Your Vue.js App"/>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'
import * as tf from '@tensorflow/tfjs';
import {loadFrozenModel} from '@tensorflow/tfjs-converter'

export default {
  name: 'app',
  components: {
    HelloWorld
  },
  methods: {
    getCatPixelz: async function() {

      const MODEL_LOC = '/web_model/tensorflowjs_model.pb'
      const WEIGHTS_LOC = '/web_model/weights_manifest.json'

      const model = await loadFrozenModel(MODEL_LOC, WEIGHTS_LOC)
      const cat = document.getElementByID('cat')
      model.execute({input: tf.fromPixels(cat)})
    
    }
  },
  mounted() {
    this.getCatPixelz()
    
    
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
