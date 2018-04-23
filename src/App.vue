<template>
  <div id="app">
    <img id='eval-img' src="./assets/logo.png">
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
    getImgPixelz: async function() {

      const MODEL_LOC = 'https://s3.amazonaws.com/web-model/image-classifier/tensorflowjs_model.pb'
      const WEIGHTS_LOC = 'https://s3.amazonaws.com/web-model/image-classifier/weights_manifest.json'

      try {
        const model = await loadFrozenModel(MODEL_LOC, WEIGHTS_LOC)
          const evalImg = tf.cast(tf.fromPixels(this.$el.querySelector("[id='eval-img']")), 'float32')
          const reshaped = evalImg.reshape([-1, 200, 100, 3])
          console.log('boutta execute')
          console.log(model)
          var output = model.execute({'Placeholder': reshaped}, 'final_result')
          console.log(output)
      }catch(err){
        console.log(err)
      }
      
    
    }
  },
  mounted() {
    this.getImgPixelz()
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
