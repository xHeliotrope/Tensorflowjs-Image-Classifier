<template>
  <div id="app">
    <h1>Image Classifier</h1>
    <p>This image classifier will evaluate an image that you take and predict which class it belongs to.</p>
    <div class="webcam-box-outer">
      <div class="webcam-box-inner">
        <video autoplay playsinline muted id="webcam" width="224" height="224"></video>
      </div>
    </div>
    <div class="wrapper">
      <div class="button" @click="evalImg()">
        <span class="button__mask"></span>
        <span class="button__text">Evaluate Human</span>
        <span class="button__text button__text--bis">Evaluate Human</span>
      </div>
    </div>
    <ul class="images">
      <li>Mario Camerena: <code>{{asPercentage(marioc)}}%</code></li>
      <li>Tay Swift: <code>{{asPercentage(tayswif)}}%</code></li>
      <li>Ultimate Warrior: <code>{{asPercentage(ultwarr)}}%</code></li>
      <li><img src="./assets/marioguitar.jpeg"></li>
      <li><img src="./assets/tswift.jpg"></li>
      <li><img src="./assets/ultwarr.jpg"></li>
    </ul>
  </div>

</template>

<script>
import HelloWorld from './components/HelloWorld.vue'
import * as tf from '@tensorflow/tfjs';
import {loadFrozenModel} from '@tensorflow/tfjs-converter'
import {Webcam} from './WebCam';

export default {
  name: 'app',
  components: {
    HelloWorld
  },
  data: function() {
    return {
      'model': null,
      'webcam': null,
      'marioc': 0,
      'tayswif': 0,
      'ultwarr': 0
    }
  },
  methods: {
    asPercentage(predictionScore) {
      return Math.floor(predictionScore * 100)
    },
    initModel: async function() {
      const MODEL_LOC = 'https://s3.amazonaws.com/web-model/image-classifier/tensorflowjs_model.pb'
      const WEIGHTS_LOC = 'https://s3.amazonaws.com/web-model/image-classifier/weights_manifest.json'
      this.model = await loadFrozenModel(MODEL_LOC, WEIGHTS_LOC)
    },
    evalImg: async function() {

      try {
          const evalImg = tf.cast(this.webcam.capture(), 'float32')
          console.log(evalImg)
          const reshaped = evalImg.reshape([-1, 112, 224, 3])
          var output = this.model.execute({'Placeholder': reshaped}, 'final_result')
          var predictions = output.dataSync()
          this.marioc = predictions[0]
          this.tayswif = predictions[1]
          this.ultwarr = predictions[2]
      }catch(err){
        console.log(err)
      }
    },
    initializeCam: async function(){
      this.webcam = new Webcam(document.getElementById('webcam'))
      await this.webcam.setup()
    }
  },
  mounted() {
    this.initModel()
    this.initializeCam()
  }
}
</script>

<style lang="scss">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

h3 {
  margin: 40px 0 0;
}
code {
  font-size: 16px;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}

ul li {
  width: 25%;
}
ul li img {
  width: 300px;
  height: 200px;
}

.webcam-box-outer {
  background: black;
  border: 1px solid #585858;
  border-radius: 4px;
  box-sizing: border-box;
  display: inline-block;
  padding: 9px;
}
.webcam-box-inner {
  border: 1px solid #585858;
  border-radius: 4px;
  box-sizing: border-box;
  display: flex;
  justify-content: center;
  overflow: hidden;
  width: 160px;
}
#webcam {
  height: 160px;
  transform: scaleX(-1);
}


.wrapper{
  margin: 0 auto;
  margin-top: 20px;
  background-color: black;
  width: fit-content;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  border-radius: 5px;
}
.button{
  touch-callout: none;
  user-select: none;
  display: inline-block;
  border: .2em solid;
  position: relative;
  cursor: pointer;
  overflow: hidden;
  opacity: 0.6;
  color: #FFF;
  &__text{
    display: block;
    padding:1em 2em;
    text-transform: uppercase;
    font-weight: bold;
    &:before{
      content: attr(title);
    }
    &--bis{
      display: block;
      position: absolute;
      top: 0; left:0; right: 0; bottom: 0;
      transform: translateX(-1 * 1em);
      opacity: 0;
    }
  }
  &__mask{
    display: block;
    position: absolute;
    top: 0; left: 0; right: 0; bottom: 0;
    background: white;
    transform: translateX(-100%) rotate(45deg);
    transition: all 0.3s;
  }  
}

.button:hover{
  opacity: 1;
  .button__text{
    animation: fx-text .3s ease-out;
    &--bis{
      animation: fx-text-bis .3s ease-out;
    }
  }    
  .button__mask{
    animation: fx-mask .3s ease-out;
  }    
}

.button:active{
  opacity: 1;
  background: white;
  color: inherit;
}



@keyframes fx-mask {
  0%{
    transform: translateX(-100%) rotate(45deg);
  }
  100%{
    transform: translateX(100%) rotate(45deg);
  }
}

@keyframes fx-text {
  0%{
    transform: translateX(0);
    opacity: 1;
  }
  100%{
    transform: translateX(1em);
    opacity: 0;
  }
}
@keyframes fx-text-bis {
  0%{
    transform: translateX(-1 * 1em);
    opacity: 0;
  }
  100%{
    transform: translateX(0);
    opacity: 1;
  }
}




</style>
