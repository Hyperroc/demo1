<template>
  <div class="com-container">
    <div class="com-chart" id="oneChart">

    </div>
  </div>
</template>

<script>
import {inject, onMounted, reactive} from "vue";

export default {
  name: "LeftTop",
  // 启动
  setup() {
    // 全局引用
    let $echarts = inject("echarts")
    let $axios = inject("axios")

    // 准备数据
    let data = reactive({})
    let x_data = reactive([])
    let y_data = reactive([])

    // 处理数据
    function setData() {
      x_data = []
      y_data = []
      for (var i in data.data) {
        x_data.push(i)
        y_data.push(data.data[i])
        console.log(i)
      }
      console.log(x_data)
      console.log(y_data)
    }

    // 访问后端获取数据
    async function getState() {
      data = await $axios.get('/test', {
        params: {
          date: '20130101',
          province: '北京市'
        }
      })
      //data = await $axios({url: '/test'})
      console.log(data.data)

    }

    // 钩子
    onMounted(() => {
      let myChart = $echarts.init(document.getElementById("oneChart"))

      // 获取数据，然后处理数据并作图
      getState().then(() => {
        setData()
        myChart.setOption({
          xAxis: {
            type: "value"
          },
          yAxis: {
            type: "category",
            data: x_data
          },
          series: [
            {
              type: "bar",
              data: y_data
            }
          ]
        })
      })
    })
    return {
      getState, data, x_data, y_data, setData
    }
  }
}
</script>

<style scoped lang="less">
.com-chart {
  width: 100%;
}
</style>