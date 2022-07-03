<template>
  <div class="com-container">
    <div class="com-chart" id="headChart"></div>
  </div>
</template>

<script>
import {inject, onMounted} from "vue";
export default {
  name: "headView",
  setup() {
    let $echarts = inject("echarts")
    let chartInstance = null
    function initChart() {
      chartInstance = $echarts.init(document.getElementById('headChart'))
      const initOption = {
        backgroundColor: '#ffffff',
        title: {
          text: '大气污染时空态势可视化分析系统',
          left: 'center',
          top: 'center',
          textStyle: {
            color: '#464646',
            fontSize: 32
          }
        },
      }
      chartInstance.setOption(initOption)
    }
    function screenAdapter() {
      // 测试算出来的 合适的字体大小
      this.titleFontSize = (document.getElementById('headChart').offsetWidth / 100) * 3.6

      const adapterOption = {
      }
      chartInstance.setOption(adapterOption)
      chartInstance.resize()
    }
    onMounted(() => {
      initChart()
      window.addEventListener('resize', screenAdapter)
    });
    return {
    }
  }
}
</script>

<style scoped lang="less">
.com-chart {
  width: auto;
}
</style>