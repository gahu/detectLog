<template>
  <section class="container">
    <ul>
      <li><router-link to="/">HOME</router-link></li>
      <li><router-link to="/chartjs">vue-chartjs</router-link></li>
      <li><router-link to="/charts">vue-charts</router-link></li>
      <li><router-link to="/chartKick">vue-chartKick</router-link></li>
    </ul>
    <h1>Demo examples of vue-charts</h1>
    <div class="row">
      <div class="col-sm-6">
        <h3>Line Chart</h3>
        <chartjs-line></chartjs-line>
      </div>
      <div class="col-sm-6">
        <h3>Bar Chart</h3>
        <chartjs-bar></chartjs-bar>
      </div>
    </div>
    <div class="row">
      <div class="col-sm-6">
        <h3>Radar Chart</h3>
        <chartjs-radar></chartjs-radar>
      </div>
      <div class="col-sm-6">
        <h3>Data Binding Line Chart</h3>
        <form @submit.prevent="addData">
          <input placeholder="Add a Label" v-model="datalabel">
          <input placeholder="Add a Data" v-model="dataentry">
          <button type="submit">Submit</button>
        </form>
        <chartjs-line :labels="labels" :data="dataset" :bind="true"></chartjs-line>
      </div>
    </div>
  </section>
</template>

<script>
  export default {
    name: 'VueCharts',
    data () {
      return {
        labels: ['January', 'February', 'March', 'April', 'May', 'June', 'July'],
        dataset: [65, 59, 80, 81, 56, 55, 40],
        datalabel: '',
        dataentry: ''
      }
    },
    methods: {
      addData () {
        this.labels.push(this.datalabel)
        this.dataset.push(this.dataentry)
        this.datalabel = ''
        this.dataentry = ''
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