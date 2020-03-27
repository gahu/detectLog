<template>
  <div>
    <h1>CHARTS</h1>
    <h3>ST</h3>
    <h5>인식 위성 수, GPS가 인식한 위성의 개수</h5>
    <button class="button is-primary" @click="fillData()">Input Data</button>
    <line-chart id="canvas-holder" :chart-data="datacollection" :chart-options="options"></line-chart>
  </div>
</template>

<script>
import LineChart from './chartwithprops'

export default {
    components: {
        LineChart
    },
    data () {
        return {
            datacollection: {
                labels: '',
                datasets: []
            },
            options: {
                responsive: true,
                maintainAspectRatio: false,
                scales: {
                    yAxes: [{
                        ticks: {
                            beginAtZero: true,
                            stepSize: 4,
                            fontSize: 14
                        }
                    }]
                }
            }
        }
    },
    mounted () {
        this.fillData()
    },
    methods: {
        fillData () {
            this.datacollection = {
                labels: this.getDatas(0),
                datasets: [
                    {
                        label: 'InUse ST',
                        backgroundColor: '#BDF5FD',
                        data: this.getDatas(1)
                    },
                    {
                        label: 'InView ST',
                        backgroundColor: '#F7F7AD',
                        data: this.getDatas(2)
                    }
                ]
            }
        },
        getDatas (number) {
            var selected = this.$store.state.pickedTang;
            var jsonTmp = this.$store.state.jsonGPSInfo[selected];
            var labels = [];
            var data1 = [];
            var data2 = [];
            
            for(var i = 0; i < jsonTmp.length; i++) {
                labels.push(jsonTmp[i].day + " || " + jsonTmp[i].time);
                var st = [0, 0];
                if(jsonTmp[i].ST != null) st = jsonTmp[i].ST.split("/");
                
                data1.push(st[0] * 1);
                data2.push(st[1] * 1);
            }

            if(number == 0) return labels;
            else if(number == 1) return data1;
            else if(number == 2) return data2;
        }
    }
}
</script>

<style scoped>
    .container {
        max-height: 100px;
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
    
    #canvas-holder {
        position: relative;
        width: 1000px;
        overflow: auto;
    }
</style>