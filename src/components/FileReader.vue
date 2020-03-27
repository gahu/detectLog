<template>
  <div>
    <label class="text-reader">
        Read File
        <!-- <input type="file" @change="loadTextFromFile"> -->
        <input type="file" id="files" name="files[]" multiple @change="loadTextFromFile"/>
        <output id="list"></output>
    </label>
    <p>몇 번째 탕 정보를 가져올까요?</p>
    <ul v-if="items">
        <li v-for="(item, index) in items" v-bind:key="item">
            <input type="radio" id="item" :value=index v-model="picked">
            <label for="item">{{item}}</label>
        </li>
    </ul>
  </div>
  <!-- <div style="visibility:hidden; opacity:0" id="dropzone">
    <div id="textnode">Drop files to add data.</div>
    
  </div> -->
</template>

<script>
export default {
  data () {
    return {
      picked: 0,
      items: '',
    }
  },
  watch: {
    picked: function (newVal, oldVal) {
      console.log('pick 변경 : ' + oldVal + '에서 ' + newVal);
      this.$store.state.pickedTang = this.picked;
    }
  },
  mounted () {
    // var self = this;
    //   window.addEventListener("dragenter", function () {
    //     document.querySelector("#dropzone").style.visibility = "";
    //     document.querySelector("#dropzone").style.opacity = 1;
    //     document.querySelector("#textnode").style.fontSize = "48px";
    //   });

    //   window.addEventListener("dragleave", function (e) {
    //     e.preventDefault();
    //     document.querySelector("#dropzone").style.visibility = "hidden";
    //     document.querySelector("#dropzone").style.opacity = 0;
    //     document.querySelector("#textnode").style.fontSize = "42px";
    //   });

    //   window.addEventListener("dragover", function (e) {
    //     e.preventDefault();
    //     document.querySelector("#dropzone").style.visibility = "";
    //     document.querySelector("#dropzone").style.opacity = 1;
    //     document.querySelector("#textnode").style.fontSize = "48px";
    //   });

    //   window.addEventListener("drop", function (e) {
    //     e.preventDefault();
    //     document.querySelector("#dropzone").style.visibility = "hidden";
    //     document.querySelector("#dropzone").style.opacity = 0;
    //     document.querySelector("#textnode").style.fontSize = "42px";
          
    //     var files = e.dataTransfer.files;
    //       console.log("Drop files:", files);
    //       //this.uploadFile(files);
    //       self.uploadFiles(files);
    //   });
  },
  methods: {
    loadTextFromFile(ev) {
      var output = [];
      var filesLength = ev.target.files.length;

      this.$store.state.jsonGPSInfo = []; // 초기화
      this.$store.state.Tang = false;
      this.$store.state.shutDownCnt = 0;
      this.$store.state.pickedTang = 0;

      var flag = false;

      // 정규표현식 정의
      var day = /^\d{2}\/\d{2}\/\d{2}/;
      var time = /^\d{2}:\d{2}:\d{2}.\d{3}/;
      var lng = /^\d{3}.\d{8}/;
      var lat = /^\d{2}.\d{8}/;
      var ST = /ST:\d{2}\/\d{2}/;
      var avSNR = /avSNR:\d{2}/;
      var HDOP = /HDOP:/;

      for(var i = 0; i < filesLength; i++) {
        const file = ev.target.files[i];
        const reader = new FileReader();

        var tmpJSONArr = [];

        var startPoint = [];
        var endPoint = [];

        reader.onload = e => {  // 이 안에서 처리해야 한다. 비동기 Promise 처리
          reader.onload = this.$emit("load", e.target.result);
          output.push(e.target.result);
          var fillData = e.target.result;
          // console.log(fillData);
          let textArr = fillData.split("\n");

          for(var j = 0; j < textArr.length; j++) {
            if(this.$store.state.Tang == false) { // 한 탕 운행중이 아닐 떄
              if(textArr[j].includes("rft_start_drive() : end")) {
                this.$store.state.Tang = true;
                console.log("tang 시작!!!!");
                tmpJSONArr = [];
                continue;
              }
            } else {  // 한 탕 운행중일 때
              if(textArr[j].includes("rft_end_drive() : end")) { // end를 만나면 한 탕 종료
                console.log("tang 종료!!!!");
                this.$store.state.Tang = false;
                this.$store.state.shutDownCnt += 1;
                this.$store.state.jsonGPSInfo.push(tmpJSONArr);
                startPoint.push(tmpJSONArr[0].day + "   " + tmpJSONArr[0].time);
                endPoint.push(tmpJSONArr[tmpJSONArr.length - 1].day + "   "
                 + tmpJSONArr[tmpJSONArr.length - 1].time);
                continue;
              }
              if(textArr[j].includes("GPS_LOG")) {
                var word = textArr[j].split(/,| |]|\(|\)/);
                var GPS = new Object();

                for(var k = 0; k < word.length; k++) {
                  if(word[k] == "") continue;
                  if(day.test(word[k])) {
                    GPS.day = word[k];
                  } else if(time.test(word[k])) {
                    GPS.time = word[k];
                  } else if(lng.test(word[k])) {
                    GPS.lng = word[k]*1;
                  } else if(lat.test(word[k])) {
                    GPS.lat = word[k]*1;
                  } else if(ST.test(word[k])) {
                    GPS.ST = word[k].substring(4, 8);
                  } else if(avSNR.test(word[k])) {
                    GPS.avSNR = word[k].substring(7, 8)*1;
                  } else if(HDOP.test(word[k])) {
                    GPS.HDOP = word[++k]*1;
                  }
                }
                
                // this.$store.state.jsonGPSInfo[this.$store.state.shutDownCnt].push({
                tmpJSONArr.push({
                  "day": GPS.day,
                  "time": GPS.time,
                  "lng": GPS.lng,
                  "lat": GPS.lat,
                  "ST": GPS.ST,
                  "avSNR": GPS.avSNR,
                  "HDOP": GPS.HDOP
                });
              }
            }
          }

          flag = true;
          this.inputArr(startPoint, endPoint);
          console.log(this.$store.state.jsonGPSInfo);
          console.log(this.$store.state.shutDownCnt);
          console.log(flag);
          if(flag) this.rerender();
        }
        
        reader.readAsText(file);
      }

      console.log(output);
    },
    inputArr(startP, endP) {
      var tmp = this.$store.state.shutDownCnt;
      this.items = [];    // 초기화
        for(var i = 0; i < tmp; i++) {
          this.items.push(i + ". " + startP[i] + " - " + endP[i]);
        }
    },
    rerender() {
      console.log('갱신!');
    }
  }
}
</script>

<style scoped>
.text-reader {
  position: relative;
  overflow: hidden;
  display: inline-block;

  /* Fancy button looking */
  border: 2px solid black;
  border-radius: 5px;
  padding: 8px 12px;
  cursor: pointer;
}
.text-reader input {
  position: absolute;
  top: 0;
  left: 0;
  z-index: -1;
  opacity: 0;
}
</style>