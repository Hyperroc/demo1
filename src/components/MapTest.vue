<template>
  <div class="map" id="mp">

  </div>
</template>

<script>
import axios from "axios"
import {onMounted, reactive, inject} from "vue";
import { getProvinceMapInfo} from "@/utils/map_utils";
import { useRouter } from "vue-router"

export default {
  name: "MapTest",
  setup() {
    const router = useRouter()
    let $echarts = inject("echarts")
    let mapData = reactive({});
    let myChart = null;
    async function getState() {
      mapData = await axios.get("http://localhost:8080/map/china.json")
    }
    function screenAdapter() {
      this.titleFontSize = (document.getElementById('mp').offsetWidth / 100) * 3.6

      const adapterOption = {
      }
      myChart.setOption(adapterOption)
      myChart.resize()
    }
    onMounted(() => {
      getState().then(() => {
        //console.log("map", mapData)
        $echarts.registerMap("china", mapData.data)
        myChart = $echarts.init(document.getElementById("mp"))
        myChart.setOption({
          backgroundColor: '#ffffff',
          geo: {
            map: "china"
          }
        })
        myChart.on('click', arg => {
          const provinceInfo = getProvinceMapInfo(arg.name)
          //console.log(provinceInfo)
          router.push({
            params: {
              province: provinceInfo.key,
              province2: arg.name,
            }
          })
        })
      })
      window.addEventListener('resize', screenAdapter)
    })
  }
}
</script>

<style scoped>
.map {
  width: 100%;
  height: 100%;
}
</style>