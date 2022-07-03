<template>
  <div class="container">
    <header class="header">
      <Head />
    </header>

    <div class="body">
      <section class="left">
        <div id="left-top">
          <AQI />

        </div>
        <div id="left-bottom">
          <Bubble />
        </div>
      </section>
      <section class="center">
        <div id="center-top">
          <section class="c-left">
            <div id="c-left-top">
              <Daily />
            </div>
            <div id="c-left-bottom">
              <MiniMap />
            </div>
          </section>
          <section class="c-right">
            <MapTest />
          </section>
        </div>
        <div id="center-middle">
          <Line />
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
import AQI from "@/components/AQI";
import Parallel from "@/components/Parallel";
import HeatMap from "@/components/HeatMap";
import Bubble from "@/components/Bubble";
import Line from "@/components/Line";
import Head from "@/components/Head";
import Daily from "@/components/Daily";
import MiniMap from "@/components/Bar";
import {inject} from "vue";

export default {
  components: {
    Ring,
    MapTest,
    AQI,
    Parallel,
    HeatMap,
    Bubble,
    Line,
    Head,
    Daily,
    MiniMap
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
    width: 28%;
    #left-top {
      height: 53%;
      //border: 1px solid brown;
      position: relative;
    }
    #left-bottom {
      height: 31%;
      //border: 1px solid brown;
      margin-top: 25px;
      position: relative;
    }
  }
  .center {
    height: 100%;
    width: 44%;
    margin-left: 0.5%;
    margin-right: 0.5%;
    #center-top {
      width: 100%;
      height: 48%;
      //border: 1px solid brown;
      position: relative;
      display: flex;
      .c-left {
        height: 100%;
        width: 30%;
        position: relative;
        #c-left-top {
          height: 49%;
          width: 100%;
          position: relative;
        }
        #c-left-bottom {
          height: 49%;
          width: 100%;
          margin-top: 12px;
          position: relative;
        }
        //border: 1px solid brown;
      }
      .c-right {
        height: 100%;
        width: 70%;
        margin-left: 1%;
        position: relative;
        //border: 1px solid brown;
      }
    }
    #center-middle {
      margin-top: 13px;
      width: 100%;
      height: 16%;
      //border: 1px solid brown;
      position: relative;
    }
    #center-bottom {
      margin-top: 12px;
      width: 100%;
      height: 20%;
      //border: 1px solid brown;
      position: relative;
    }
  }
  .right {
    height: 100%;
    width: 28%;
    #right-top {
      height: 46%;
      //border: 1px solid brown;
      position: relative;
    }
    #right-bottom {
      height: 38%;
      margin-top: 25px;
      //border: 1px solid brown;
      position: relative;
    }
  }
}
</style>