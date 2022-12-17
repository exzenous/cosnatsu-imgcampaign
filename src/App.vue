<script>

import { fabric } from 'fabric'
import xmasFrame from './assets/Xmas-Photoframe.png'
// import tickImg from './assets/check.png'
import heic2any from "heic2any";

export default {
  data() {
    const preImages = import.meta.glob("./assets/pre-stickers/*")
    const images = import.meta.glob("./assets/stickers/*")
    
    var preStickers = []
    for (const path in preImages) {
      preStickers.push(this.getImageUrl(path))
    }
    
    var stickers = []
    for (const path in images) {
      stickers.push(this.getImageUrl(path))
    }

    return {
      preStickers: preStickers,
      stickers: stickers,
      imageCanvas: null,
      overlayImg: null,
      yourName: "",
      yourNameObj: null,
      yourChar: "",
      yourCharObj: null,
      base: null,
      cosnatsuCurrentImage: xmasFrame,
      objectiveNames: [
        { name: "มาคอส", isSet: false, posX: 58, posY: 448, tick: null },
        { name: "มาถ่าย", isSet: false, posX: 138, posY: 448, tick: null },
        { name: "มาขาย", isSet: false, posX: 222, posY: 448, tick: null },
        { name: "มากิน", isSet: false, posX: 308, posY: 448, tick: null },
        { name: "มาเล่น", isSet: false, posX: 58, posY: 476, tick: null },
        { name: "มาเที่ยว", isSet: false, posX: 138, posY: 476, tick: null },
        { name: "มาหาเธอ", isSet: false, posX: 222, posY: 476, tick: null },
        { name: "มาทำไม?", isSet: false, posX: 308, posY: 476, tick: null }
      ]
    }
  },
  methods: {

    deselectCanvas(){
      this.imageCanvas.discardActiveObject().renderAll();
    },
    getImageUrl(url) {
      return (new URL(url, import.meta.url).href)
    },
    saveImage(e) {
      const canvas = document.getElementById('imgCanvas')
      const link = document.createElement('a');
      canvas.toBlob(blob => {
        let URLObj = window.URL || window.webkitURL;
        link.download = 'download.png';
        link.href = URLObj.createObjectURL(blob);
        link.click();
        URLObj.revokeObjectURL(link.href)
        link.delete;
      }, 'image/png');
    },
    textChange(event) {
      const source = event.target || event.srcElement

      if (source.id == "nameTextField") {
        if (this.yourName.length > 16) {
          this.yourNameObj.set({ fontSize: 24, top: 300 })
        } else {
          this.yourNameObj.set({ fontSize: 33, top: 294 })
        }
        this.yourNameObj.text = this.yourName
      }
      else {
        if (this.yourChar.length > 16) {
          this.yourCharObj.set({ fontSize: 24, top: 370 })
        } else {
          this.yourCharObj.set({ fontSize: 33, top: 362 })
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
      } else {
        this.imageCanvas.fxRemove(item.tick)
        item.tick = null
      }
    },
    addTick(infoItem) {
      var circle = new fabric.Circle({
        radius: 8, fill: 'green', left: infoItem.posX, top: infoItem.posY, selectable: false
      });
      infoItem.tick = circle
      this.imageCanvas.add(circle)

      // fabric.Image.fromURL(tickImg, (tick) => {
      //   infoItem.tick = tick
      // }, {
      //   radius: 8, fill: 'green', left: infoItem.posX, top: infoItem.posY, selectable: false, scaleX: 0.1, scaleY: 0.1
      // })
    },
    addSticker(stickerToAdd) {
      fabric.Image.fromURL(stickerToAdd, (img) => {
        this.imageCanvas.add(img)
        this.refreshIndex()
      }, {scaleX: 0.2, scaleY: 0.2})
      this.refreshIndex()
    },
    changeBaseImage(target) {
      this.cosnatsuCurrentImage = target
      fabric.Image.fromURL(this.cosnatsuCurrentImage, (img) => {
        img.set({ scaleX: 0.5, scaleY: 0.5 })
        if (this.base != null) {
          this.imageCanvas.remove(this.base)
        }
        this.base = img
        this.base.set('selectable', false)
        this.imageCanvas.add(this.base)
        this.refreshIndex()
      })
    },
    loadBaseImage() {
      this.imageCanvas = new fabric.Canvas('imgCanvas')
      this.imageCanvas.backgroundColor = "white"
      this.imageCanvas
        .setDimensions({ width: 960, height: 540 }, { backstoreOnly: true })
        .setDimensions({ width: '100%', height: 'inherit' }, { cssOnly: true })
      const canvas = document.getElementById('imgCanvas')
      canvas.style.position = 'relative'
      this.changeBaseImage(this.cosnatsuCurrentImage)
    },
    previewFile(event) {
      var reader = new FileReader()
      let extension = null
      try {
        extension = event.target.files[0].name.split(".").pop()
      } catch {
      }
      if ((["heic", "heif"].indexOf(extension) > -1)) {
        let photo = event.target.files[0];
        heic2any({
          blob: photo,
          toType: 'image/jpeg',
          quality: 0.7
        }).then(blob => {
          reader.readAsDataURL(blob)
        }, error => {});
      }
      reader.onload = (f) => {
        let result = f.target.result
        if (result.includes("image/")) {

          fabric.Image.fromURL(f.target.result, (img) => {

            //frame y => 11 to 456 = 445px
            //frame x => 450 to 930 = 480px

            this.overlayImg = img
            this.imageCanvas.add(this.overlayImg)

            if (img.width / img.height > 1) {
              // Landscape
              const rescaleFac = 440 / img.width
              const centreValueLeft = 485 + 205 - (rescaleFac * img.width / 2)
              const top = (540 - (img.height * rescaleFac)) / 2 - 36
              this.overlayImg.set({ top: top, left: centreValueLeft, scaleX: rescaleFac, scaleY: rescaleFac })
            } else {
              // Square, Portrait
              const rescaleFac = 410 / img.height
              const centreValueLeft = 485 + 205 - (rescaleFac * img.width / 2)
              this.overlayImg.set({ top: 25, left: centreValueLeft, scaleX: rescaleFac, scaleY: rescaleFac })
            }
            this.refreshIndex()
          })
        }

      }
      if (this.overlayImg != null) {
        this.imageCanvas.remove(this.overlayImg)
        this.overlayImg = null
        reader.readAsDataURL(event.target.files[0])
      }
      else {
        reader.readAsDataURL(event.target.files[0])
      }
    },
    refreshIndex() {
      if (this.overlayImg != null) {
        this.imageCanvas.moveTo(this.overlayImg, 0)
      }
      this.imageCanvas.moveTo(this.yourNameObj, 2)
      this.imageCanvas.moveTo(this.yourCharObj, 3)
      this.imageCanvas.renderAll()
    }
  },
  mounted() {
    this.loadBaseImage()

    this.yourNameObj = new fabric.Text('', { left: 50, top: 294, fontSize: 33, fontFamily: 'Sriracha, cursive', selectable: false });
    this.imageCanvas.add(this.yourNameObj);
    this.yourCharObj = new fabric.Text('', { left: 50, top: 362, fontSize: 33, fontFamily: 'Sriracha, cursive', selectable: false });
    this.imageCanvas.add(this.yourCharObj)
    this.refreshIndex()

    document.addEventListener('click', this.deselectCanvas);

  }
}
</script>

<template>
  <!-- Header -->
  <div class="tw- h-24 tw-p-4">
    <img class="" style="width: 80px;" src="icon.jpg" >
  </div>

  <!-- App -->
  <div id="appmodule" class="tw-flex tw-flex-wrap tw-mt-6 tw-mb-16">

    <div id="canvasWrapper" class="tw-flex tw-flex-wrap tw-place-items-center tw-px-4">
      <canvas id="imgCanvas" class="tw-drop-shadow-xl tw-rounded-md" style="width: 100% !important;"></canvas>
      <!-- Sticker Pane -->
      <div class="tw-flex tw-overflow-x-scroll tw-mt-12 tw-w-[100%]">
          <img v-for="i,index in preStickers" :src="i" @click="addSticker(stickers[index])" style="width:100px;height: 100px;" alt="">
      </div>
    </div>
    <div class="tw-container tw-p-8 tw-pt-10">

      <div class="d-flex flex-column justify-content-center flex-wrap py-4">
        <div class="form-floating mb-4">
          <input type="text" class="form-control" id="nameTextField" placeholder=" " @input="textChange"
            v-model="yourName" />
          <label for="nameTextField">Your Name</label>
        </div>
        <div class="form-floating mb-4">
          <input type="text" class="form-control" id="characterTextField" placeholder=" " @input="textChange"
            v-model="yourChar" />
          <label for="characterTextField">Your Character</label>
        </div>

        <div>
          <label for="floatingInput" class="mb-2">Your Picture</label>
          <input type="file" class="form-control" accept="image/*, .heif, .heic" id="floatingInput"
            @input="previewFile" />
        </div>
      </div>

      <div class="d-flex justify-content-center flex-wrap">
        <div v-for="i in objectiveNames" class="p-1">
          <input type="checkbox" class="btn-check" :id="i.name" @click="setTick(i)" /><label
            class="btn btn-outline-success" :for="i.name">{{ i.name }}</label>
        </div>
      </div>

      <div class="row py-4">
        <a class="btn btn-success" @click="saveImage">Save</a>
      </div>

    </div>
    
  </div>
  <!-- App -->

  <!-- Footer -->
  <div class="tw-p-4 tw-m-2 tw-bg-slate-50 tw-rounded-lg row">

    <div class="col">
      <p>Image/Artwork Resources <br> Courtesy of Cosnatsu Event. or its affiliates.</p>
    </div>

    <div class="col text-end">
      <p>Open Sourced - Nathachai B.</p>
      <h2><a href="https://github.com/exzenous/cosnatsu-imgcampaign"><i class="fa fa-github"></i></a></h2>
    </div>
    <i><p><small>PDPA Concerns: <br> Your Image is entirely processed on your client, your data will NOT be stored anywhere else.</small></p></i>
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
