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
    let month=[];
    let myChart = null;

    for (let i = 2013; i < 2019; i++) {
      for (let j = 1; j < 13; j++) {
        month.push(i*100+j);
      }
    }

    async function initData() {
      const cityUrl = 'http://localhost:8080/static/City/month_' + route.params.province + '.json';
      $axios.get(cityUrl).then(response =>  {
        //console.log(response.data)
        myChart = $echarts.init(document.getElementById("onChart"), 'chalk')
        myChart.setOption({
          backgroundColor: '#ffffff',
          graphic: [{ //环形图中间添加文字
            type: 'text', //通过不同top值可以设置上下显示
            left: 'center',
            top: 'center',
            style: {
              text: route.params.province2,
              fill: '#000', //文字的颜色
              fontSize: 20,
            }
          }
          ],
          title: {
            text: '省份月均数据',
            left: 'center',
            padding: 10,
            itemGap: 0,
            textStyle: {
              fontSize: 20,
              color: '#000'
            },
            subtext: '当前省份72个月的月均数据',
          },
          legend: {
            top: 'bottom',
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
      })
    }
    function screenAdapter() {
      // 测试算出来的 合适的字体大小
      this.titleFontSize = (document.getElementById('onChart').offsetWidth / 100) * 3.6

      const adapterOption = {
      }
      myChart.setOption(adapterOption)
      myChart.resize()
    }
    // 钩子
    onMounted(() => {
      initData()
      window.addEventListener('resize', screenAdapter)
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