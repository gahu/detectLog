<script>
import {Bar, mixins} from 'vue-chartjs'

const { reactiveProp } = mixins

export default {
    extends: Bar,
    mixins: [reactiveProp],
    data () {
        return {
            // Chart.js options that control the appearance of the chart
            options: {
                scales: {
                    yAxes: [{
                        ticks: {
                            beginAtZero: true
                        },
                        gridLines: {
                            display: true
                        }
                    }],
                    xAxes: [{
                        gridLines: {
                            display: false
                        }
                    }]
                },
                legend: {
                    display: true
                },
                responsive: true,
                maintainAspectRatio: false
            }
        }
    },
    mounted () {
        // this.chartData is created in the mixin and contains all the data needed to build the chart.
        this.renderChart(this.chartData, this.options)
    }
}
</script>