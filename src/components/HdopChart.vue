<template>
  <div>
    <h3>HDOP</h3>
    <h5>수평위치오차값</h5>
    <h6>일반적으로 ＜ 2 : 매우 우수, ＜ 4 : 우수, ＜ 6 : 보통, ＞6 : 측위 부적합</h6>
    <button class="button is-primary" @click="chartSet()">Input Data</button>
    <line-chart :data="chartData" :options="options"></line-chart>
  </div>
</template>

<script>
export default {
    name: 'VueChartKick',
    data () {
        return {
            chartData: [],
            options: {
                responsive: true,
                maintainAspectRatio: false,
                scales: {
                    yAxes: [{
                        ticks: {
                        }
                    }]
                }
            }
            // options: {
            //     onClick:function(e) {
            //         var activePoints = LineChart.getElementAtEvent(e);
            //         var selectedIndex = activePoints[0]._index;
            //         alert(this.data.datasets[0].data[selectedIndex]);
            //     }
            // }
        }
    },
    created() {
        // this.chartSet();
    },
    methods : {
        chartSet() {
            var selected = this.$store.state.pickedTang;
            var jsonTp = this.$store.state.jsonGPSInfo[selected];
            this.chartData = [];
            
            for(var i = 0; i < jsonTp.length; i++) {
                this.chartData.push([jsonTp[i].day + " || " + jsonTp[i].time, jsonTp[i].HDOP]);
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