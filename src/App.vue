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

      const MODEL_LOC = 'https://s3.amazonaws.com/web-model/image-classifier/tensorflowjs_model.pb'
      const WEIGHTS_LOC = 'https://s3.amazonaws.com/web-model/image-classifier/weights_manifest.json'

      try {
        const model = await loadFrozenModel(MODEL_LOC, WEIGHTS_LOC)
        console.log(model)
        const cat = this.$el.querySelector("[id='cat']")
        model.execute({input: {"Placeholder": tf.fromPixels(cat)}})
        console.log(model)
      }catch(err){
        console.log(err)
      }
      
    
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
