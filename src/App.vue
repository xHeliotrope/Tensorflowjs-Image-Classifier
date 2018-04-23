<template>
  <div id="app">
    <img id='eval-img' src="./assets/marioguitar.jpeg">
    <HelloWorld msg="Welcome to Your Vue.js App"/>
    <ul>
      <li>Predicted Mario Camerena: {{marioc}}</li>
      <li>Predicted Taylor Swift: {{tayswif}}</li>
      <li>Predicted Ultimate Warrior: {{ultwarr}}</li>
    </ul>
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
  data: function() {
    return {
      'marioc': 0,
      'tayswif': 0,
      'ultwarr': 0
    }
  },
  methods: {
    getImgPixelz: async function() {

      const MODEL_LOC = 'https://s3.amazonaws.com/web-model/image-classifier/tensorflowjs_model.pb'
      const WEIGHTS_LOC = 'https://s3.amazonaws.com/web-model/image-classifier/weights_manifest.json'

      try {
        const model = await loadFrozenModel(MODEL_LOC, WEIGHTS_LOC)
          const evalImg = tf.cast(tf.fromPixels(this.$el.querySelector("[id='eval-img']")), 'float32')
          const reshaped = evalImg.reshape([-1, 150, 168, 3])
          var output = model.execute({'Placeholder': reshaped}, 'final_result')
          var predictions = output.dataSync()
          this.marioc = predictions[0]
          this.tayswif = predictions[1]
          this.ultwarr = predictions[2]
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
