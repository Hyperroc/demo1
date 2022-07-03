<template>
  <div class="com-container">
    <div class="com-chart" id="bbChart"></div>
  </div>
</template>

<script>
import {inject, onMounted, reactive, watchEffect} from "vue";
import {useRoute} from "vue-router"

export default {
  name: "bubbleChart",
  setup() {
    let $echarts = inject("echarts")
    let $axios = inject("axios")
    let chartInstance = null
    let allData = reactive({})
    const route = useRoute()
    function initChart() {
      chartInstance = $echarts.init(document.getElementById('bbChart'))
      const initOption = {}
      chartInstance.setOption(initOption)
    }
    async function getData() {
      const cityUrl = 'http://localhost:8080/static/Cluster/kmeans_cluster_for_' + route.params.province + '.json';
      allData = await $axios.get(cityUrl)
      //console.log("bbb", allData.data)
      updateChart()
    }
    function updateChart() {
      const dataOption = {
        backgroundColor: '#ffffff',
        grid: {
          top: 50
        },
        title: {
          text: '时间片聚类散点图',
          left: 'center',
          padding: 10,
          itemGap: 2,
          textStyle: {
            fontSize: 20,
            color: '#000'
          },
          subtext: 'AQI>150'
        },
        toolbox:{
          feature:{
            dataZoom:{},
            restore: {},
          },
        },
        tooltip: {
          position: 'top',
          trigger: 'item',
          formatter: function(data){
            return '年份:' + data.value[2] + '<br />' + '日期:'+ String(data.value[4]).slice(4) + '<br />' + 'AQI: ' + data.value[1];
          },
          triggerOn: "mousemove|click",
          borderWidth: 0,
        },
        visualMap: {
          show: false,
          type: 'piecewise',
          categories: ['0', '1', '2', '3', '4', '5'],
          dimension: 5,
          orient: 'horizontal',
          top: 'middle',
          left: 'center',
          inRange: {
            color: ['#ec1c24', '#ffca18', '#0ed145','#b83dba','#3f48cc','#b97a56']
          },
        },
        xAxis: {
          show: true,
        },
        yAxis: {
          type: "value",
          show: true,
          min: 120,
          max: 550
        },
        series: [
          {
            symbolSize: 8,
            data: allData.data,
            type: 'scatter',
            itemStyle: {
              borderColor: '#555'
            },
          }
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