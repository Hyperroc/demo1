<template>
  <div class="com-container">
    <div class="com-chart" id="dailyChart"></div>
  </div>
</template>

<script>
import {inject, onMounted, reactive} from "vue";

export default {
  name: "DailyChart",
  setup() {
    let $echarts = inject("echarts")
    let $axios = inject("axios")
    let chartInstance = null
    let allData = reactive({})
    const centerArr = [
      ['18%', '40%'],
      ['50%', '40%'],
      ['82%', '40%'],
      ['34%', '75%'],
      ['66%', '75%'],
    ]
    function initChart() {
      chartInstance = $echarts.init(document.getElementById('dailyChart'))
      const initOption = {}
      chartInstance.setOption(initOption)
    }
    async function getData() {
      allData = await $axios.get('/test', {
        params: {
          date: '20130101',
          province: '北京市'
        }
      })
      updateChart()
    }
    function updateChart() {
      const seriesArr = allData.data.data.map((item, index) => {
        return {
          type: 'pie',
          radius: [50, 40],
          center: centerArr[index],
          hoverAnimation: false,
          labelLine: {
            show: false
          },
          label: {
            position: 'center'
          },
          data: [
            {
              value: item.value,
              itemStyle: {
                color: new $echarts.graphic.LinearGradient(0, 1, 0, 0, [
                  {
                    offset: 0,
                    color: '#4ff778'
                  },
                  {
                    offset: 1,
                    color: '#0ba82c'
                  }
                ])
              }
            },
            {
              name: item.name + '\n' + item.value,
              value: 100,
              itemStyle: {
                color: '#ccd1cb'
              }
            },
          ]
        }
      })
      const dataOption = {
        series: seriesArr
      }
      chartInstance.setOption(dataOption)
    }
    onMounted(() => {
      initChart()
      getData()
    })
    return {
      allData
    }
  }
}
</script>

<style scoped lang="less">

</style>