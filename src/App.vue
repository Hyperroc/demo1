<template>
  <router-view></router-view>
</template>

<script>
import {onMounted, provide} from "vue"
import * as echarts from "echarts"
import axios from "axios"
import {useRouter} from "vue-router"

  axios.defaults.baseURL = "http://localhost:5000"
  export default {
    setup() {
      provide("echarts", echarts)
      provide("axios", axios)
      const router = useRouter()

      onMounted(() => {
        router.push({
          name: 'index',
          params: {
            province: 'changchun',
            province2: '吉林',
            time: '20130101'
          }
        })
      })
    }
  }
</script>

<style>
body {
  background: #e7e8ef;
}
</style>
