<template>
  <div class="com-container">
    <div class="com-chart" id="onChart"></div>
  </div>
</template>

<script>
import {inject, onMounted, watchEffect} from "vue";
import {useRoute} from "vue-router"

export default {
  name: "RingChart",
  setup() {
    let $echarts = inject("echarts")
    let $axios = inject("axios")
    const route = useRoute()
    const month = 1;

    async function initData() {
      const cityUrl = 'http://localhost:8080/static/City/month_' + route.params.province + '.json';
      $axios.get(cityUrl).then(response =>  {
        console.log(response.data)
        let myChart = $echarts.init(document.getElementById("onChart"), 'chalk')
        myChart.setOption({
          graphic: [{ //环形图中间添加文字
            type: 'text', //通过不同top值可以设置上下显示
            left: 'center',
            top: 'center',
            style: {
              text: route.params.province,
              fill: '#000', //文字的颜色
              fontSize: 12,
            }
          }
          ],
          title: {
            text: '城市月均数据',
            padding: 5,
            itemGap: 0,
            textStyle: {
              fontSize: 20,
              color: '#000'
            },
            subtext: '数据来自中国高分辨率大气污染再分析数据集',
            sublink: 'http://naq.cicidata.top:10443/chinavis/opendata'
          },
          legend: {
            data: ['TEMP', {
              name: 'RH',
              lineStyle: 'inherit'
            }, 'PSFC', 'AQI', 'PM2.5', 'PM10', 'NO2,CO', 'CO', 'O3', 'NO2'],
            itemStyle: 'inherit',
            lineStyle: 'inherit',
            orient: "vertical",
            top: "middle",
            right: "0%",
          },
          polar: {
            center: ['50%', '50%'],
            radius: ["25%", '80%']
          },
          tooltip: {
            trigger: 'axis',
            formatter: function (params) {
              return params[0].name + '<br/>' +
                  'TEMP:' + String(params[0].data - 273) + '℃' + '<br/>' +
                  'RH:' + String(params[1].data - 240) + '%' + '<br/>' +
                  'PSFC:' + String(Math.floor(params[2].data * 3.8)) + 'hPA' + '<br/>'
            },
          },
          angleAxis: {
            type: 'category',
            boundaryGap: true,
            axisTick: {
              alignWithLabel: true
            },
            startAngle: 90,
            data: month,
            // show: false
            axisLine: {
              lineStyle: {
                type: "dashed"
              }
            },
            axisPointer: {
              type: "shadow",
              label: {
                show: false,
                formatter: function (params) {
                  return params.value
                }
              }
            }
          },
          radiusAxis: {
            min: 120,
            max: 330,
            show: false
          },
          series: [
            {
              coordinateSystem: 'polar',
              name: 'TEMP',
              type: 'line',
              smooth: true,
              showSymbol: false,
              data: response.data.TEMP,
              label: {
                show: false
              },
              z: 10
            },
            {
              coordinateSystem: 'polar',
              name: 'RH',
              type: 'line',
              smooth: true,
              showSymbol: false,
              data: response.data.RH,
              lineStyle: {
                color: "#8cd9ff"
              }
            },
            {
              coordinateSystem: 'polar',
              name: 'PSFC',
              type: 'line',
              smooth: true,
              showSymbol: false,
              data: response.data.PSFC,
              lineStyle: {
                color: "#fff200"
              }
            },
            {
              name: 'AQI_month',
              type: 'pie',
              center: ['50%', '50%'],
              radius: ["25%", '30%'],
              z: 100,
              label: {
                show: false
              },
              data: response.data.aqilevel,
              itemStyle: {
                borderWidth: 1,
                borderColor: "#fff"
              }
            },
            {
              name: 'PPNCO',
              type: 'pie',
              center: ['50%', '50%'],
              radius: ["30%", '35%'],
              z: 100,
              label: {
                show: false
              },
              data: response.data.PPNCO,
              itemStyle: {
                borderWidth: 1,
                borderColor: "#fff"
              }
            },
            {
              name: 'NO2',
              type: 'pie',
              center: ['50%', '50%'],
              radius: ["52%", '57%'],
              z: 100,
              label: {
                show: false
              },
              data: response.data.NO2,
              itemStyle: {
                borderWidth: 1,
                borderColor: "#fff"
              }
            },
            {
              name: 'O3',
              type: 'pie',
              center: ['50%', '50%'],
              radius: ["47%", '52%'],
              z: 100,
              label: {
                show: false
              },
              data: response.data.O3,
              itemStyle: {
                borderWidth: 1,
                borderColor: "#fff"
              }
            },
            {
              name: 'CO',
              type: 'pie',
              center: ['50%', '50%'],
              radius: ["42%", '47%'],
              z: 100,
              label: {
                show: false
              },
              data: response.data.CO,
              itemStyle: {
                borderWidth: 1,
                borderColor: "#fff"
              }
            }
          ],
        });
        window.onresize = function () {
          myChart.resize()
        }
      })
    }
    // 钩子
    onMounted(() => {
      initData()
      return {
        initData
      }
    });
    // 监视
    watchEffect(() => {
      initData()
    })
  }
}
</script>

<style scoped lang="less">
.com-chart {
  width: 100%;
}
</style>