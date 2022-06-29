<template>
  <div class="com-container">
    <div class="com-chart" id="heatChart"></div>
  </div>
</template>

<script>
import {inject, onMounted, reactive} from "vue";
import {useRoute} from "vue-router"

export default {
  name: "HeatMap",
  setup() {
    let $echarts = inject("echarts")
    let $axios = inject("axios")
    let chartInstance = null
    let allData = reactive({})
    let x_day;
    const data=[];
    const y_category = [
      'AQI', 'PM2.5', 'PM10',
      'SO2', 'NO2', 'CO', 'O3'
    ];

    function select_x_day(day) {
      const str = day.slice(0, 4) + "/" + day.slice(4, 6) + "/" + day.slice(6);
      const date = new Date(str);
      let cur = Date.parse(str);
      let firstdate = Date.parse('2013/01/01');
      let diffDate = cur - firstdate;
      if(diffDate<1000*3600*24*3){
        x_day = ['20130101','20130102','20130103','20130104','20130105','20130106','20130107']
        return 1;
      }
      for(var i =-3;i<4;i++){
        var curdate = new Date(str);
        var month = '';
        var daytime = '';
        curdate.setDate(date.getDate()+i);
        if(curdate.getMonth()<10){
          month = '0' + (curdate.getMonth()+1).toString();
        }else{
          month = (curdate.getMonth()+1).toString();
        }
        if(curdate.getDate()<10){
          daytime = '0' + curdate.getDate().toString();
        }else{
          daytime = curdate.getDate().toString();
        }
        var curstr = curdate.getFullYear().toString() + month + daytime;
        x_day.push(curstr);
      }
    }

    const route = useRoute()
    function initChart() {
      chartInstance = $echarts.init(document.getElementById('heatChart'))
      const initOption = {}
      chartInstance.setOption(initOption)
    }
    async function getData() {
      x_day.splice(0);
      select_x_day(route.params.time)
      const cityUrl = 'http://localhost:8080/static/City/' + route.params.province + '.json';
      allData = await $axios.get(cityUrl)

      const day = route.params.time;
      const str = day.slice(0,4) + "/" + day.slice(4,6) + "/" + day.slice(6)
      let cur = Date.parse(str);
      let firstdate = Date.parse('2013/01/01');
      let diffDate = Math.abs(cur - firstdate);
      let index = Math.floor(diffDate / (1000 * 3600 * 24));
      if(index < 3){
        index = 3
      }
      for(var j = 0;j<7;j++){
        data.push([j, 0,allData.data[index+j-3][0]]);
        data.push([j, 1,Math.floor(allData.data[index+j-3][1])]);
        data.push([j, 2,Math.floor(allData.data[index+j-3][2])]);
        data.push([j, 3,Math.floor(allData.data[index+j-3][3])]);
        data.push([j, 4,Math.floor(allData.data[index+j-3][4])]);
        data.push([j, 5,Math.floor(allData.data[index+j-3][5]/0.12)]);
        data.push([j, 6,Math.floor(allData.data[index+j-3][6])]);
      }

      updateChart()
    }
    function updateChart() {
      const dataOption = {
        title: {
          text: '矩形热力图',
          top: 4,
          padding: 5,
          itemGap: 2,
          textStyle: {
            fontSize: 14,
            color: '#000'
          },
          subtext: '七天之内的污染数据'
        },
        tooltip: {
          position: 'top'
        },
        grid: {
          height: "70%",
          width: "85%",
          top: 40
        },
        xAxis: {
          type: 'category',
          data: x_day,
          splitArea: {
            show: true
          }
        },
        yAxis: {
          type: 'category',
          data: y_category,
          splitArea: {
            show: true
          }
        },
        visualMap: {
          show: false,
          type: "piecewise",
          min: 0,
          max: 300,
          calculable: true,
        },
        series: [
          {
            name: 'Pollution',
            type: 'heatmap',
            data: data,
            label: {
              show: true
            },
            emphasis: {
              itemStyle: {
                shadowBlur: 10,
                shadowColor: 'rgba(0, 0, 0, 0.5)'
              }
            },
            itemStyle: {
              borderColor: "#fff",
              borderWidth: 2
            },
          },
        ]
      };
      chartInstance.setOption(dataOption)
      window.onresize = function () {
        chartInstance.resize()
      }
    }
    onMounted(() => {
      select_x_day(route.params.time)
      initChart()
      getData()
    })
    return {
      allData
    }
  }
}
</script>

<style scoped>

</style>