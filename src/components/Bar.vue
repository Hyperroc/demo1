<template>
  <div class="com-container">
    <div class="com-chart" id="barChart"></div>
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
    let cols = []
    const route = useRoute()
    function initChart() {
      chartInstance = $echarts.init(document.getElementById('barChart'))
      const initOption = {}
      chartInstance.setOption(initOption)
    }
    async function getData() {
      allData = await $axios.get('/test4', {
        params: {
          date: route.params.time,
          province: route.params.province2
        }
      })
      allData.data.data[4].value /= 1000
      allData.data.data[2].value -= 273
      if (cols.length == 0) {
        for (let i = 0; i < 5; i++) {
          cols.push(allData.data.data[i].name)
        }
      }
      updateChart()
    }
    function updateChart() {
      const dataOption = {
        backgroundColor: '#fff',
        title: {
          text: '日均气候指标',
          left: 'center',
          padding: 10,
          itemGap: 2,
          textStyle: {
            fontSize: 20,
            color: '#000'
          },
        },
        tooltip: {
          trigger: 'item'
        },
        xAxis: {
          type: 'category',
          data: cols
        },
        yAxis: {
          type: 'value'
        },
        series: [
          {
            data: allData.data.data,
            type: 'bar',
            showBackground: true,
            backgroundStyle: {
              color: 'rgba(180, 180, 180, 0.2)'
            }
          }
        ]
      };
      chartInstance.setOption(dataOption)
    }
    function screenAdapter() {
      // 测试算出来的 合适的字体大小
      this.titleFontSize = (document.getElementById('barChart').offsetWidth / 100) * 3.6

      const adapterOption = {

      }
      chartInstance.setOption(adapterOption)
      chartInstance.resize()
    }
    onMounted(() => {
      initChart()
      getData()
      window.addEventListener('resize', screenAdapter)
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