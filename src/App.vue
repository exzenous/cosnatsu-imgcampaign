<script>

  import { fabric } from 'fabric'
  import img1 from './assets/cosnatsu-t1-1.png'
  import img2 from './assets/cosnatsu-t2-1.png'

  export default {
    data() {
      return {
        imageCanvas: null,
        overlayImg: null,
        yourName: "",
        yourNameObj: null,
        yourChar: "",
        yourCharObj: null,
        base: null,
        cosnatsuImages: [
          img1,
          img2
        ],
        cosnatsuCurrentImage: img1,
        objectiveNames: [
          {name:"มาคอส", isSet: false, posX: 58, posY: 448, tick: null},
          {name:"มาถ่าย", isSet: false, posX: 138, posY: 448, tick: null},
          {name:"มาขาย", isSet: false,  posX: 222, posY: 448, tick: null},
          {name:"มากิน", isSet: false,  posX: 308, posY: 448, tick: null},
          {name:"มาเล่น", isSet: false,  posX: 58, posY: 476, tick: null},
          {name:"มาเที่ยว", isSet: false,  posX: 138, posY: 476, tick: null},
          {name:"มาหาเธอ", isSet: false,  posX: 222, posY: 476, tick: null},
          {name:"มาทำไม?", isSet: false,  posX: 308, posY: 476, tick: null}
        ]
      }
    },
    methods: {
      saveImage(e) {
        const a = document.getElementById('imgCanvas')
        const link = document.createElement('a');
        link.download = 'download.png';
        link.href = a.toDataURL();
        link.click();
        link.delete;
      },
      textChange(event){
        const source = event.target || event.srcElement

        if (source.id == "nameTextField") {
          if (this.yourName.length > 16) {
            this.yourNameObj.set({fontSize: 24, top: 300})
          }else {
            this.yourNameObj.set({fontSize: 33, top: 294})
          }
          this.yourNameObj.text = this.yourName
        }
        else {
          if (this.yourChar.length > 16) {
            this.yourCharObj.set({fontSize: 24, top: 370})
          }else {
            this.yourCharObj.set({fontSize: 33, top: 362})
          }
          this.yourCharObj.text = this.yourChar
        }
        this.refreshIndex()
        this.imageCanvas.renderAll()
      },
      setTick(item) {
        item.isSet = !item.isSet

        if (item.isSet) {
          this.addTick(item)
        }else{
          this.imageCanvas.fxRemove(item.tick)
          item.tick = null
        }
      },
      addTick(infoItem) {
        var circle = new fabric.Circle({
          radius: 8, fill: 'green', left: infoItem.posX, top: infoItem.posY
        });

        infoItem.tick = circle
        this.imageCanvas.add(circle)
      },
      changeBaseImage(target) {
        this.cosnatsuCurrentImage = target
        fabric.Image.fromURL(this.cosnatsuCurrentImage, (img) =>{
          img.set({scaleX: 0.5, scaleY: 0.5})
          this.imageCanvas.remove(this.base)
          //this.imageCanvas.setBackgroundImage(img, this.imageCanvas.renderAll.bind(this.imageCanvas), {scaleX: 0.5, scaleY: 0.5})
          this.base = img
          this.imageCanvas.add(this.base)
          this.refreshIndex()
        })
      },
      loadBaseImage() {
        this.imageCanvas = new fabric.StaticCanvas('imgCanvas')
        this.imageCanvas.backgroundColor="white"
        this.imageCanvas
        .setDimensions({width: 960, height: 540}, {backstoreOnly: true})
        .setDimensions({width: '100%', height: 'inherit'}, {cssOnly: true})
        this.changeBaseImage(this.cosnatsuCurrentImage)
      },
       previewFile(event) {
        var reader = new FileReader()
        reader.onload = (f) => {

          fabric.Image.fromURL(f.target.result, (img) => {

            //frame y => 11 to 456 = 445px
            //frame x => 450 to 930 = 480px

            this.overlayImg = img
            this.imageCanvas.add(this.overlayImg)

            if (img.width/img.height > 1){
              // Landscape
              //const rescaleFac = 410/img.width
              const rescaleFac = 480/img.width
              const centreValueLeft = 485+205-(rescaleFac*img.width/2)
              const top = (540 - (img.height*rescaleFac)) / 2 - 36
              this.overlayImg.set({top: top, left: centreValueLeft, scaleX: rescaleFac , scaleY: rescaleFac})
            }else {
              // Square, Portrait
              //const rescaleFac = 340/img.height
              const rescaleFac = 445/img.height
              const centreValueLeft = 485+205-(rescaleFac*img.width/2)
              this.overlayImg.set({top: 11, left: centreValueLeft, scaleX: rescaleFac , scaleY: rescaleFac})
            }
            this.refreshIndex()
            //this.imageCanvas.renderAll()
          })
          
        }
        if (this.overlayImg != null) {
          this.imageCanvas.remove(this.overlayImg)
          //this.refreshIndex()
          this.overlayImg = null
          reader.readAsDataURL(event.target.files[0])
        }
        else {
          reader.readAsDataURL(event.target.files[0])
          //this.refreshIndex()
        }
      },
      refreshIndex() {
        if (this.overlayImg != null){
          this.imageCanvas.moveTo(this.overlayImg, 0)
        }
        this.imageCanvas.moveTo(this.base, 1)
        this.imageCanvas.moveTo(this.yourNameObj, 2)
        this.imageCanvas.moveTo(this.yourCharObj, 3)
        this.imageCanvas.renderAll()
      }
    },
    mounted() {
      this.loadBaseImage()
      
      this.yourNameObj = new fabric.Text('', { left: 50, top: 294, fontSize: 33 , fontFamily: 'Sriracha, cursive' });
      this.imageCanvas.add(this.yourNameObj);
      this.yourCharObj = new fabric.Text('', { left: 50, top: 362, fontSize: 33 , fontFamily: 'Sriracha, cursive' });
      this.imageCanvas.add(this.yourCharObj)
      this.refreshIndex()
      this.changeBaseImage(this.cosnatsuCurrentImage)

      }
    }
</script>

<template>

  <!-- App -->
  <div id="appmodule" class="d-flex flex-row py-4 mt-5">

    <div id="canvasWrapper" class="d-flex flex-wrap align-items-center">
      <canvas id="imgCanvas" class="shadow rounded" style="width: 100% !important;"></canvas>
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
          <input type="text" class="form-control" id="nameTextField" placeholder=" " @input="textChange" v-model="yourName"/>
          <label for="nameTextField">Your Name</label>
        </div>
        <div class="form-floating mb-4">
          <input type="text" class="form-control" id="characterTextField" placeholder=" " @input="textChange" v-model="yourChar" />
          <label for="characterTextField">Your Character</label>
        </div>

        <div>
          <label for="floatingInput" class="mb-2">Your Picture</label>
          <input type="file" class="form-control" accept="image/*" id="floatingInput" @input="previewFile" />
        </div>
      </div>

      <div class="d-flex justify-content-center flex-wrap">
        <div v-for="i in objectiveNames" class="p-1">
          <input type="checkbox" class="btn-check" :id="i.name" @click="setTick(i)"/><label class="btn btn-outline-info" :for="i.name">{{ i.name }}</label>
        </div>
      </div>

      <div class="row py-4">
        <a class="btn btn-info" @click="saveImage">Save</a>
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
