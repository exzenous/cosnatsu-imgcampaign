<script>
  export default {
    data() {
      return {
        currentImage: "",
        cosnatsuImages: [],
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
      getLog(a) {
        console.log(a)
      },
    },
    mounted() {

    },
    computed: {

    }
  }
</script>

<template>

  <!-- App -->
  <div id="appmodule" class="d-flex flex-row py-4 mt-5">

    <div class="d-flex flex-wrap align-items-center">
      <img class="shadow rounded" style="width:100%" src="./assets/CosAndPlay-Template.png">
    </div>
    <div class="container p-4 pt-5">
      
      <div class="row">
        <div class="btn-group">
          <input @click="getLog('ssss')" type="radio" class="btn-check" name="mode" id="btnradio1" checked>
          <label class="btn btn-outline-info" for="btnradio1">CosNatsu</label>
          <input @click="getLog('aaaa')" type="radio" class="btn-check" name="mode" id="btnradio2">
          <label class="btn btn-outline-info" for="btnradio2">Halloween</label>
        </div>
      </div>
      
      <div class="d-flex flex-column justify-content-center flex-wrap py-4">
        <div class="form-floating mb-4">
          <input type="text" class="form-control" id="floatingInput" placeholder="name@example.com"/>
          <label for="floatingInput">Your Name</label>
        </div>
        <div class="form-floating mb-4">
          <input type="text" class="form-control" id="floatingInput" placeholder="name@example.com"/>
          <label for="floatingInput">Your Character</label>
        </div>
        <div class="">
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
      <h2><a href="https://www.github.com/exzenous"><i class="fa fa-github"></i></a></h2>
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