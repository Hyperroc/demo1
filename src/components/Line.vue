<template>
  <div class="com-container">
    <div class="com-chart" id="lineChart"></div>
  </div>
</template>

<script>
import {inject, onMounted, reactive, watchEffect} from "vue";
import {useRoute, useRouter} from "vue-router"
import {date_convert} from "@/utils/date_utils";

export default {
  name: "lineChart",
  setup() {
    let $echarts = inject("echarts")
    let $axios = inject("axios")
    let chartInstance = null
    let allData = reactive({})
    const route = useRoute()
    const router = useRouter()
    let oneDay = 24 * 3600 * 1000;
    let flag = 0;
    function initData() {
      let date = [];
      let data = [Math.abs(Math.random() * 300)];
      let base = +new Date(2012, 11, 31);
      for (let i = 1; i < 2192; i++) {
        let now = new Date((base += oneDay));
        date.push([now.getFullYear(), now.getMonth() + 1, now.getDate()].join('/'));
        data.push(Math.abs(Math.round((Math.random() - 0.5) * 20 + data[i - 1])));
      }
      return {'data':data, 'date':date}
    }

    function initChart() {
      chartInstance = $echarts.init(document.getElementById('lineChart'))
      const initOption = {}
      chartInstance.setOption(initOption)
      flag = 1
    }
    async function getData() {
      const cityUrl = 'http://localhost:8080/static/City/month_' + route.params.province + '.json';
      allData = await $axios.get(cityUrl)
      updateChart(initData())
    }
    function updateChart(arg) {
      const dataOption = {
        backgroundColor: '#ffffff',
        title: {
          text: 'AQI时间轴',
          left: 'center',
          padding: 10,
          itemGap: 2,
          textStyle: {
            fontSize: 20,
            color: '#000'
          },
          subtext: '2013-2018日均AQI'
        },
        tooltip: {
          trigger: 'axis',
          position: function (pt) {
            return [pt[0], '10%'];
          }
        },
        xAxis: {
          type: 'category',
          boundaryGap: false,
          triggerEvent: true,
          data: arg.date
        },
        yAxis: {
          type: 'value',
          boundaryGap: [0, '100%']
        },
        dataZoom: [
          {
            type: 'inside',
            start: 0,
            end: 10
          },
          {
            start: 0,
            end: 10
          }
        ],
        series: [
          {
            name: 'AQI',
            type: 'line',
            symbol: 'none',
            sampling: 'lttb',
            itemStyle: {
              color: 'rgb(255, 70, 131)'
            },
            areaStyle: {
              color: new $echarts.graphic.LinearGradient(0, 0, 0, 1, [
                {
                  offset: 0,
                  color: 'rgb(255, 158, 68)'
                },
                {
                  offset: 1,
                  color: 'rgb(255, 70, 131)'
                }
              ])
            },
            data: arg.data
          }
        ]
      };
      chartInstance.setOption(dataOption)
      chartInstance.on('click', arg => {
        //const provinceInfo = getProvinceMapInfo(arg.name)
        router.push({ params: { time: date_convert(arg.value) } })
        //console.log("click:", date_convert(arg.value))
      })
      window.onresize = function () {
        chartInstance.resize()
      }
    }
    function screenAdapter() {
      // 测试算出来的 合适的字体大小
      this.titleFontSize = (document.getElementById('lineChart').offsetWidth / 100) * 3.6

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
      console.log(route.params.province)
      if (flag) {
        updateChart(initData())
      }
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