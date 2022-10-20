<script>

  import { fabric } from 'fabric'

  export default {
    data() {
      return {
        imageCanvas: null,
        overlayImg: null,
        yourName: "",
        yourChar: "",
        currentImage: "",
        cosnatsuImages: [
          'https://cdn.cosnatsu.com/wp-content/uploads/2022/10/18011359/CosAndPlay-Template-1.png',
          'https://cdn.cosnatsu.com/wp-content/uploads/2022/10/18185836/CosAndPlay-Halloween-1.png'
        ], //['',''],
        objectiveNames: [
          "มาคอส",
          "มาถ่าย",
          "มาขาย",
          "มากิน",
          "มาเล่น",
          "มาเที่ยว",
          "มาหาเธอ",
          "มาทำไม?"
        ]
      }
    },
    methods: {
      textChange(){
        console.log(this.yourName, this.yourChar)
      },
      changeBaseImage(target) {
        fabric.Image.fromURL(target, (img) =>{
          this.imageCanvas.setBackgroundImage(img, this.imageCanvas.renderAll.bind(this.imageCanvas), {scaleX: 0.5, scaleY: 0.5})
        })
      },
      loadBaseImage() {
        this.imageCanvas = this.$refs.imgCanvas
        this.imageCanvas = new fabric.StaticCanvas('imgCanvas', {})

        this.imageCanvas
        .setDimensions({width: 960, height: 540}, {backstoreOnly: true})
        .setDimensions({width: '100%', height: 'inherit'}, {cssOnly: true})
        
        this.changeBaseImage(this.cosnatsuImages[0])

        var circle = new fabric.Circle({
          radius: 20, fill: 'green', left: 100, top: 100
        });
        var triangle = new fabric.Triangle({
          width: 20, height: 30, fill: 'blue', left: 50, top: 50
        });

        this.imageCanvas.add(circle, triangle)
      }
    },
    mounted() {
      this.loadBaseImage()
    }
  }
</script>

<template>

  <!-- App -->
  <div id="appmodule" class="d-flex flex-row py-4 mt-5">

    <div id="canvasWrapper" class="d-flex flex-wrap align-items-center">
      <canvas id="imgCanvas" ref="imgCanvas" class="shadow rounded" style="width: 100% !important;"></canvas>
    </div>
    <div class="container p-4 pt-5">
      
      <div class="row">
        <div class="btn-group">
          <input @click="changeBaseImage(this.cosnatsuImages[0])" type="radio" class="btn-check" name="mode" id="btnradio1" checked>
          <label class="btn btn-outline-info" for="btnradio1">CosNatsu</label>
          <input @click="changeBaseImage(this.cosnatsuImages[1])" type="radio" class="btn-check" name="mode" id="btnradio2">
          <label class="btn btn-outline-info" for="btnradio2">Halloween</label>
        </div>
      </div>
      
      <div class="d-flex flex-column justify-content-center flex-wrap py-4">
        <div class="form-floating mb-4">
          <input type="text" class="form-control" id="nameTextField" placeholder=" " @input="textChange()" v-model="yourName"/>
          <label for="nameTextField">Your Name</label>
        </div>
        <div class="form-floating mb-4">
          <input type="text" class="form-control" id="characterTextField" placeholder=" " @input="textChange()" v-model="yourChar" />
          <label for="characterTextField">Your Character</label>
        </div>

        <div>
          <label for="floatingInput" class="mb-2">Your Picture</label>
          <input type="file" class="form-control " id="floatingInput" />
        </div>
      </div>

      <div class="d-flex justify-content-center flex-wrap">
        <div v-for="i in objectiveNames" class="p-1">
          <input type="checkbox" class="btn-check" :id="i"/><label class="btn btn-outline-info" :for="i">{{ i }}</label>
        </div>
      </div>

      <div class="row py-4">
        <button class="btn btn-info">Save</button>
      </div>

    </div>

  </div>
  <!-- App -->

  <!-- Footer -->
  <div class="p-4 m-2 bg-light rounded-3 row">
    
    <div class="col">
      <p>Image/Artwork Resources <br> Courtesy of Cosnatsu Event. or its affiliates.</p>
    </div>
    
    <div class="col text-end">
      <p>Open Sourced - Nathachai B.</p>
      <h2><a href="https://github.com/exzenous/cosnatsu-imgcampaign"><i class="fa fa-github"></i></a></h2>
    </div>
  </div>


</template>

<style>
@media only screen and (max-width: 1200px) {
  #appmodule {
    flex-direction: column !important;
  }
}

@media only screen and (min-width: 1200px) {
  #appmodule>div:nth-child(1) {
    width: 70%;
  }
  #appmodule>div:nth-child(2) {
    width: 30%;
  }
}

</style>