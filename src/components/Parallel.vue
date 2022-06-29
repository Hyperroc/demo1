<template>
  <div class="com-container">
    <div class="com-chart" id="parChart"></div>
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
    const schema = [
      { name: 'date', index: 0, text: '日期' },
      { name: 'PM25', index: 1, text: 'PM2.5' },
      { name: 'PM10', index: 2, text: 'PM10' },
      { name: 'SO2', index: 3, text: 'SO2' },
      { name: 'NO2', index: 4, text: 'NO2' },
      { name: 'CO', index: 5, text: ' CO' },
      { name: 'AQIindex', index: 6, text: 'AQI' },
      { name: '等级', index: 7, text: '等级' }
    ];
    function initChart() {
      chartInstance = $echarts.init(document.getElementById('parChart'))
      const initOption = {}
      chartInstance.setOption(initOption)
    }
    async function getData() {
      allData = await $axios.get('/test3', {
        params: {
          date: '201301',
          province: route.params.province2
        }
      })
      console.log('hello', allData)
      updateChart()
    }
    function updateChart() {
      const dataOption = {
        backgroundColor: '#333',
        legend: {
          bottom: 30,
          data: route.params.province,
          itemGap: 20,
          textStyle: {
            color: '#fff',
            fontSize: 14
          }
        },
        tooltip: {
          padding: 10,
          backgroundColor: '#222',
          borderColor: '#777',
          borderWidth: 1
        },
        parallelAxis: [
          {
            dim: 0,
            name: schema[0].text,
            inverse: true,
            max: 31,
            nameLocation: 'start'
          },
          { dim: 1, name: schema[1].text },
          { dim: 2, name: schema[2].text },
          { dim: 3, name: schema[3].text },
          { dim: 4, name: schema[4].text },
          { dim: 5, name: schema[5].text },
          { dim: 6, name: schema[6].text },
          {
            dim: 7,
            name: schema[7].text,
            type: 'category',
            data: ['优', '良', '轻度', '中度', '重度', '严重']
          }
        ],
        visualMap: {
          show: true,
          min: 0,
          max: 150,
          dimension: 2,
          inRange: {
            color: ['#d94e5d', '#eac736', '#50a3ba'].reverse()
            // colorAlpha: [0, 1]
          }
        },
        parallel: {
          left: '5%',
          right: '18%',
          bottom: 100,
          parallelAxisDefault: {
            type: 'value',
            name: 'AQI指数',
            nameLocation: 'end',
            nameGap: 20,
            nameTextStyle: {
              color: '#fff',
              fontSize: 12
            },
            axisLine: {
              lineStyle: {
                color: '#aaa'
              }
            },
            axisTick: {
              lineStyle: {
                color: '#777'
              }
            },
            splitLine: {
              show: false
            },
            axisLabel: {
              color: '#fff'
            }
          }
        },
        series: {
          name: 'Beijing',
          type: 'parallel',
          lineStyle: lineStyle,
          data: allData.data.data
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