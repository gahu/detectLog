<template>
  <!-- <div :style="{backgroundImage:'url(' + require('@/../public/img/DataAnalysis.jpg') + ')'
                , backgroundSize: 'cover'}"> -->
  <div>
    <div class="home" id="Home">
      <!-- <img alt="Vue logo" src="../assets/logo.png"> -->
      <br>
      <textarea rows="30" cols="100" v-model="text"></textarea>
      <br>
      <file-reader @load="text = $event"></file-reader>
      <Dmap>
        <div id="map" />
      </Dmap>
      <section class="container">
        <div class="row">
          <ST></ST>
        </div>
        <div class="row">
          <AVSNR></AVSNR>
        </div>
        <div class="row">
          <HDOP></HDOP>
        </div>
      </section>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import FileReader from '@/components/FileReader.vue';
import Dmap from "@/components/VueDMap.vue";
import ST from "@/components/STChart.vue";
import AVSNR from '@/components/avsnrChart.vue';
import HDOP from '@/components/HdopChart.vue';

export default {
  name: 'Home',
  data: () => ({ text: "" }),
  components: {
    FileReader,
    Dmap,
    ST,
    AVSNR,
    HDOP
  }
}
</script>

<style scoped lang="scss">
label {
  display: inline;
  align-items: center;
  cursor: pointer;
}
</style>