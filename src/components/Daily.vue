<template>
  <div class="com-container">
    <div class="com-chart" id="dailyChart"></div>
  </div>
</template>

<script>
import {inject, onMounted, reactive, watchEffect} from "vue";
import {useRoute} from "vue-router"
export default {
  name: "DailyChart",
  setup() {
    let $echarts = inject("echarts")
    let $axios = inject("axios")
    let chartInstance = null
    let allData = reactive({})
    let allData2 = reactive({})
    const route = useRoute()

    function initChart() {
      chartInstance = $echarts.init(document.getElementById('dailyChart'))
      const initOption = {}
      chartInstance.setOption(initOption)
    }
    async function getData() {
      allData = await $axios.get('/test', {
        params: {
          date: route.params.time,
          province: route.params.province2
        }
      })
      allData2 = await $axios.get('/test4', {
        params: {
          date: route.params.time,
          province: route.params.province2
        }
      })
      allData.data.data.pop()
      console.log('data2', allData2)
      updateChart()
    }
    function updateChart() {
      const dataOption = {
        backgroundColor: '#fff',
        tooltip: {
          trigger: 'item'
        },
        title: {
          text: '日均污染指标',
          left: 'center',
          padding: 10,
          itemGap: 2,
          textStyle: {
            fontSize: 20,
            color: '#000'
          },
        },
        legend: {
          top: '10%',
          left: 'center',
        },
        series: [
          {
            name: 'Access From',
            type: 'pie',
            radius: ['40%', '70%'],
            center: ['50%', '60%'],
            avoidLabelOverlap: false,
            itemStyle: {
              borderRadius: 10,
              borderColor: '#fff',
              borderWidth: 2
            },
            label: {
              show: false,
              position: 'center'
            },
            emphasis: {
              label: {
                show: true,
                fontSize: '40',
                fontWeight: 'bold'
              }
            },
            labelLine: {
              show: false
            },
            data: allData.data.data
          },
        ]
      };
      chartInstance.setOption(dataOption)
    }
    function screenAdapter() {
      // 测试算出来的 合适的字体大小
      this.titleFontSize = (document.getElementById('dailyChart').offsetWidth / 100) * 3.6

      const adapterOption = {
      }
      chartInstance.setOption(adapterOption)
      chartInstance.resize()
    }
    onMounted(() => {
      initChart()
      getData()
      window.addEventListener('resize', screenAdapter)
      //screenAdapter()
    })
    watchEffect(() => {
      getData()
      //screenAdapter()
    })
    return {
      allData
    }
  }
}
</script>

<style scoped lang="less">

</style>