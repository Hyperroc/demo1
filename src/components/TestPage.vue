<template>
  <div class="chart" id="oneChart">

  </div>
</template>

<script>
import {inject, onMounted, reactive} from "vue";

export default {
  name: "TestPage",
  setup() {
    let $echarts = inject("echarts")
    let $axios = inject("axios")

    let data = reactive({})
    let x_data = reactive([])
    let y_data = reactive([])

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

    async function getState() {
      data = await $axios({url: '/test'})
      console.log(data.data)

    }

    onMounted(() => {
      let myChart = $echarts.init(document.getElementById("oneChart"))
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

<style scoped>
.chart {
  height: 4.5rem;
}
</style>