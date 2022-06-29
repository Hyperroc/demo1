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
    async function getState() {
      mapData = await axios.get("http://localhost:8080/map/china.json")
    }
    onMounted(() => {
      getState().then(() => {
        console.log("map", mapData)
        $echarts.registerMap("china", mapData.data)
        let myChart = $echarts.init(document.getElementById("mp"))
        myChart.setOption({
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