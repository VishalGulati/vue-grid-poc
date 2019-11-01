<template>
    <v-card flat color="grey lighten-5">
        <v-card-text>
            <div class="echarts">
                <Echarts :options="chartOptions" autoresize/>
            </div>
            <!--<v-btn @click="doRandom" small outline fab color="grey">
                                                                                                  <v-icon>refresh</v-icon>
                                                                                                </v-btn>-->
        </v-card-text>
    </v-card>
</template>

<script>
import Echarts from 'vue-echarts'
import 'echarts/lib/chart/line'
import 'echarts/lib/component/title'

export default {
  name: 'lineChart',
  components: {
    Echarts
  },
  data: function () {
    return {
      resizable: true,
      loading: false,
      chartOptions: {
        title: {
          text: 'Line Chart'
        },
        xAxis: {
          type: 'category',
          data: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun']
        },
        yAxis: {
          type: 'value'
        },
        itemStyle: {
          color: '#1867c0'
        },
        series: [
          {
            data: [520, 582, 701, 834, 990, 830, 720],
            type: 'line',
            smooth: false
          }
        ]
      }
    }
  },
  props: {
    title: String
  },
  methods: {
    periodicRandom () {
      setInterval(() => this.doRandom(), 3000)
    },
    doRandom () {
      const that = this
      let randomVal = Math.floor(Math.random() * (600 + 1 - 300) + 300)

      that.chartOptions.series[0].data.shift()
      that.chartOptions.series[0].data.push(randomVal)
    },
    onReady (instance, ECharts) {
      // console.log(instance, ECharts);
    },
    onClick (event, instance, ECharts) {
      // console.log(arguments)
    }
  },
  created () {
    this.periodicRandom()
  }
}
</script>

<style>
.echarts {
    overflow-y: hidden;
    overflow-x: hidden;
    width: 100%;
}
</style>
