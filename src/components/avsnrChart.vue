<template>
  <div>
    <h3>avSNR</h3>
    <h5>SNR값이 0 초과인 위성 개수의 평균 SNR값</h5>
    <button class="button is-primary" @click="inputData()">Input Data</button>
    <chartjs-bar :labels="labels" :data="dataset" :options="options" :bind="true"></chartjs-bar>
  </div>
</template>

<script>
  export default {
    name: 'VueCharts',
    data () {
      return {
        labels: [],
        dataset: [],
        options: {

        }
      }
    },
    methods: {
      inputData () {
          var selected = this.$store.state.pickedTang;
          var tmp = this.$store.state.jsonGPSInfo[selected];

          this.labels = [];
          this.dataset = [];

          for(var i = 0; i < tmp.length; i++) {
              this.labels.push(tmp[i].day + " || " + tmp[i].time);
              this.dataset.push(tmp[i].avSNR);
          }
      }
    }
  }
</script>

<style scoped>
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
</style>