<template>
  <div class="container">
    <header class="header">

    </header>

    <div class="body">
      <section class="left">
        <div id="left-top">
          <AQI />

        </div>
        <div id="left-bottom">
          <MapTmp />
        </div>
      </section>
      <section class="center">
        <div id="center-top">
          <MapTest />
          <ElTest />
        </div>
        <div id="center-bottom">
          <Parallel />
        </div>
      </section>
      <section class="right">
        <div id="right-top">
          <Ring />
        </div>
        <div id="right-bottom">
          <HeatMap />
        </div>
      </section>

    </div>

  </div>
</template>

<script>
import Ring from "@/components/Ring";
import MapTest from "@/components/MapTest";
//import Daily from "@/components/Daily";
import AQI from "@/components/AQI";
import Parallel from "@/components/Parallel";
import HeatMap from "@/components/HeatMap";
import MapTmp from "@/components/MapTmp";
import ElTest from "@/components/ElTest";
import {inject} from "vue";

export default {
  components: {
    Ring,
    MapTest,
    AQI,
    Parallel,
    HeatMap,
    MapTmp,
    ElTest
  },
  setup() {
    let $echarts = inject("echarts")
    let $axios = inject("axios")
    console.log($echarts)
    console.log($axios)
  }
}
</script>

<style lang="less">
.container {
  width: 100%;
  height: 100%;
  padding: 0 20px;
  box-sizing: border-box;
}
.header {
  width: 100%;
  height: 64px;
  font-size: 20px;
  position: relative;
}
.body {
  width: 100%;
  height: 100%;
  display: flex;
  margin-top: 10px;
  .left {
    height: 100%;
    width: 27.6%;
    #left-top {
      height: 53%;
      border: 1px solid brown;
      position: relative;
    }
    #left-bottom {
      height: 31%;
      border: 1px solid brown;
      margin-top: 25px;
      position: relative;
    }
  }
  .center {
    height: 100%;
    width: 41.5%;
    margin-left: 1.6%;
    margin-right: 1.6%;
    #center-top {
      width: 100%;
      height: 56%;
      border: 1px solid brown;
      position: relative;
    }
    #center-bottom {
      margin-top: 25px;
      width: 100%;
      height: 28%;
      border: 1px solid brown;
      position: relative;
    }
  }
  .right {
    height: 100%;
    width: 27.6%;
    #right-top {
      height: 46%;
      border: 1px solid brown;
      position: relative;
    }
    #right-bottom {
      height: 38%;
      margin-top: 25px;
      border: 1px solid brown;
      position: relative;
    }
  }
}
</style>