<template>
  <div class="com-container">
    <div class="com-chart" id="aqiChart"></div>
  </div>
</template>

<script>
import {inject, onMounted, reactive, watchEffect} from "vue";
import {useRoute} from "vue-router"

export default {
  name: "aqiChart",
  setup() {
    let $echarts = inject("echarts")
    let $axios = inject("axios")
    let chartInstance = null
    let allData = reactive({})
    const route = useRoute()
    const lineStyle = {
      width: 1,
      opacity: 0.5
    }
    function initChart() {
      chartInstance = $echarts.init(document.getElementById('aqiChart'))
      const initOption = {}
      chartInstance.setOption(initOption)
    }
    async function getData() {
      allData = await $axios.get('/test2', {
        params: {
          date: '201301',
          province: route.params.province2
        }
      })
      updateChart()
    }
    function updateChart() {
      const dataOption = {
        backgroundColor: '#161627',
        title: {
          text: 'AQI - Radar',
          left: 'center',
          textStyle: {
            color: '#eee'
          }
        },
        legend: {
          bottom: 5,
          data: ['Beijing', 'Shanghai', 'Guangzhou'],
          itemGap: 20,
          textStyle: {
            color: '#fff',
            fontSize: 14
          },
          selectedMode: 'single'
        },
        radar: {
          indicator: [
            { name: 'PM2.5', max: 350 },
            { name: 'PM10', max: 500 },
            { name: 'SO2', max: 200 },
            { name: 'NO2', max: 200 },
            { name: 'CO', max: 15 },
            { name: 'AQI', max: 400 },
          ],
          shape: 'circle',
          splitNumber: 5,
          axisName: {
            color: 'rgb(238, 197, 102)'
          },
          splitLine: {
            lineStyle: {
              color: [
                'rgba(238, 197, 102, 0.1)',
                'rgba(238, 197, 102, 0.2)',
                'rgba(238, 197, 102, 0.4)',
                'rgba(238, 197, 102, 0.6)',
                'rgba(238, 197, 102, 0.8)',
                'rgba(238, 197, 102, 1)'
              ].reverse()
            }
          },
          splitArea: {
            show: false
          },
          axisLine: {
            lineStyle: {
              color: 'rgba(238, 197, 102, 0.5)'
            }
          }
        },
        series: {
          name: route.params.province,
          type: 'radar',
          lineStyle: lineStyle,
          data: allData.data.data,
          symbol: 'none',
          itemStyle: {
            color: '#F9713C'
          },
          areaStyle: {
            opacity: 0.1
          }
        }
      };
      chartInstance.setOption(dataOption)
      window.onresize = function () {
        chartInstance.resize()
      }
    }
    onMounted(() => {
      initChart()
      getData()
    });
    watchEffect(() => {
      getData()
    })
    return {
      allData
    }
  }
}
</script>

<style scoped lang="less">
.com-chart {
  width: auto;
}
</style>