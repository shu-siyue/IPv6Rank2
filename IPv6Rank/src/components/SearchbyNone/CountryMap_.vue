<template>
  <div class="countrymap clearfix">
    <div class="left">
      <ul class="tabs">
        <li v-for="(item, index) in selected" :key="index" @click="selectTab(index)" :class="{ active: isSelectedTab === index }">
          {{ item }}
        </li>
      </ul>
      <hr />
      <div class="switch" v-if="isSelectedTab === 0">
        <button :class="{ activeButton: isSelectedButton === 0 }" @click="drawSwitchMapIPv61()">allocated space</button>
        <button :class="{ activeButton: isSelectedButton === 1 }" @click="drawSwitchMapIPv62()">cc allocated space</button>
        <button :class="{ activeButton: isSelectedButton === 2 }" @click="drawSwitchMapIPv63()">advertised space</button>
      </div>
      <div class="switch" v-else-if="isSelectedTab === 1">
        <button :class="{ activeButton: isSelectedButton === 0 }" @click="drawSwitchMapAS1()">allocated space</button>
        <button :class="{ activeButton: isSelectedButton === 1 }" @click="drawSwitchMapAS2()">cc allocated space</button>
      </div>
      <div id="map" class="map"></div>
    </div>
    <div class="right">
      <div style="position: relative">
        <!-- <h4 class="right-title">Introduction</h4> -->
        <div v-if="isSelectedTab === 0" class="desc">
          <h4 class="intro">v6地址在各个国家的资源分配情况</h4>
          <div class="intro_item"><i class="el-icon-caret-right"></i>allocated space:<br> 各个国家已分配v6资源在全球的占比情况，其中占比最高的是美国（9.02%），其次是中国（8.59%）。</div>
          <div class="intro_item"><i class="el-icon-caret-right"></i>cc allocated space:<br> 各个国家可路由v6地址占已分配v6资源的比例。</div>
          <div class="intro_item"><i class="el-icon-caret-right"></i>advertised space:<br> 各个国家可路由v6地址在全球的占比情况，其中占比最高的是美国（23.56%）。</div>
        </div>
        <div v-else-if="isSelectedTab === 1" class="desc">
          <h4 class="intro">AS号数量在各个国家的分配情况</h4>
          <div class="intro_item"><i class="el-icon-caret-right"></i>allocated space:<br> 各个国家已分配AS号数量在全球的占比情况，其中占比最高的是美国（28.60%）。</div>
          <div class="intro_item"><i class="el-icon-caret-right"></i>cc allocated space:<br> 各个国家可路由AS号占已分配AS号的比例。</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
var realDatav61 = require("@/utils/realDatav61.json");
var realDatav62 = require("@/utils/realDatav62.json");
var realDatav63 = require("@/utils/realDatav63.json");
var realDataAS1 = require("@/utils/realDataAS1.json");
var realDataAS2 = require("@/utils/realDataAS2.json");

export default {
  name: "countryMap",
  data() {
    return {
      selected: ["IPv6", "AS Number"],
      isSelectedTab: 0,
      isSelectedButton: 0,
    };
  },
  mounted() {
    this.drawSwitchMapIPv61();
  },
  methods: {
    selectTab(index) {
      this.isSelectedTab = index;
      this.isSelectedButton = 0;
      if (index === 0) {
        this.drawSwitchMapIPv61();
      } else {
        this.drawSwitchMapAS1();
      }
    },
    drawSwitchMapIPv61() {
      this.isSelectedButton = 0;
      let map = this.$echarts.init(document.getElementById("map"));
      map.setOption({
        tooltip: {
          trigger: "item",
          formatter: function (params) {
            var value = params.value + "";
            return (
              "% Allocated IPv6 space" + "<br/>" + params.name + " : " + value*100 + "%"
            );
          },
        },
        visualMap: {
          min: 0.0,
          max: 0.1,
          text: ["High", "Low"],
          precision: 3, //设置小数精度，默认0没有小数
          realtime: false,
          calculable: true,
          color: ["MediumPurple", "Lavender"],
          itemWidth: 13,
          itemHeight: 80,
          left: 10,
          top: 170,
        },
        series: [
          {
            type: "map",
            mapType: "world",
            top: 45,
            left: 50,
            zoom: 1.2,
            roam: false,
            itemStyle: {
              emphasis: {
                label: {
                  show: true,
                },
              },
            },
            data: realDatav61,
          },
        ],
      });
    },
    drawSwitchMapIPv62() {
      this.isSelectedButton = 1;
      let map = this.$echarts.init(document.getElementById("map"));
      map.setOption({
        tooltip: {
          trigger: "item",
          formatter: function (params) {
            var value = params.value + "";
            return (
              "% cc Allocated IPv6 space" + "<br/>" + params.name + " : " + value*100 + "%"
            );
          },
        },
        visualMap: {
          min: 0.0,
          max: 1.0,
          text: ["High", "Low"],
          precision: 3, //设置小数精度，默认0没有小数
          realtime: false,
          calculable: true,
          color: ["MediumPurple", "Lavender"],
          itemWidth: 13,
          itemHeight: 80,
          left: 10,
          top: 170,
        },
        series: [
          {
            type: "map",
            mapType: "world",
            top: 45,
            left: 50,
            zoom: 1.2,
            roam: false,
            itemStyle: {
              emphasis: {
                label: {
                  show: true,
                },
              },
            },
            data: realDatav62,
          },
        ],
      });
    },
    drawSwitchMapIPv63() {
      this.isSelectedButton = 2;
      let map = this.$echarts.init(document.getElementById("map"));
      map.setOption({
        tooltip: {
          trigger: "item",
          formatter: function (params) {
            var value = params.value + "";
            return (
              "% Advertised IPv6 space" + "<br/>" + params.name + " : " + value*100 + "%"
            );
          },
        },
        visualMap: {
          min: 0.0,
          max: 0.25,
          text: ["High", "Low"],
          precision: 3, //设置小数精度，默认0没有小数
          realtime: false,
          calculable: true,
          color: ["MediumPurple", "Lavender"],
          itemWidth: 13,
          itemHeight: 80,
          left: 10,
          top: 170,
        },
        series: [
          {
            type: "map",
            mapType: "world",
            top: 45,
            left: 50,
            zoom: 1.2,
            roam: false,
            itemStyle: {
              emphasis: {
                label: {
                  show: true,
                },
              },
            },
            data: realDatav63,
          },
        ],
      });
    },
    drawSwitchMapAS1() {
      this.isSelectedButton = 0;
      let map = this.$echarts.init(document.getElementById("map"));
      map.setOption({
        tooltip: {
          trigger: "item",
          formatter: function (params) {
            var value = params.value + "";
            return (
              "% Allocated AS space" + "<br/>" + params.name + " : " + (value*100).toFixed(2) + "%"
            );
          },
        },
        visualMap: {
          min: 0.0,
          max: 0.3,
          text: ["High", "Low"],
          precision: 3, //设置小数精度，默认0没有小数
          realtime: false,
          calculable: true,
          color: ["MediumPurple", "Lavender"],
          itemWidth: 13,
          itemHeight: 80,
          left: 10,
          top: 170,
        },
        series: [
          {
            type: "map",
            mapType: "world",
            top: 45,
            left: 50,
            zoom: 1.2,
            roam: false,
            itemStyle: {
              emphasis: {
                label: {
                  show: true,
                },
              },
            },
            data: realDataAS1,
          },
        ],
      });
    },
    drawSwitchMapAS2() {
      this.isSelectedButton = 1;
      let map = this.$echarts.init(document.getElementById("map"));
      map.setOption({
        tooltip: {
          trigger: "item",
          formatter: function (params) {
            var value = params.value + "";
            return (
              "% cc Allocated AS space" + "<br/>" + params.name + " : " + (value*100).toFixed(2) + "%"
            );
          },
        },
        visualMap: {
          min: 0.0,
          max: 1.0,
          text: ["High", "Low"],
          precision: 3, //设置小数精度，默认0没有小数
          realtime: false,
          calculable: true,
          color: ["MediumPurple", "Lavender"],
          itemWidth: 13,
          itemHeight: 80,
          left: 10,
          top: 170,
        },
        series: [
          {
            type: "map",
            mapType: "world",
            top: 45,
            left: 50,
            zoom: 1.2,
            roam: false,
            itemStyle: {
              emphasis: {
                label: {
                  show: true,
                },
              },
            },
            data: realDataAS2,
          },
        ],
      });
    },
  },
};
</script>

<style lang="scss" scoped>
@import "~@/assets/css/tabpage.css";
.tabs {
  width: 90%;
  margin-top: 10px;
  display: flex;
  font-size: 13px;
  justify-content: center;
  li {
    margin-left: 25px;
    margin-bottom: 0px;
    padding-left: 5px;
    padding-right: 5px;
    padding-top: 1px;
    padding-bottom: 1px;
    border: 1px solid rgb(22, 22, 110);
    border-bottom: transparent;
    background-color: #ffffff;
    cursor: pointer;
  }
  .active {
    border-bottom: 1px solid white;
    background-color: #dfdfdf;
    cursor: auto;
  }
}
.switch {
  margin-top: 20px;
  display: flex;
  justify-content: center;
  button {
    margin-right: 10px;
    margin-left: 10px;
    padding: 1px;
    padding-right: 3px;
    padding-left: 3px;
    border: 1.3px solid gray;
    border-radius: 3px;
    background: rgb(255, 255, 255);
    cursor: pointer;
  }
  button:focus {
    outline: none;
  }
  .activeButton {
    background-color: #dfdfdf;
    cursor: auto;
  }
}
hr {
  margin-left: 10px;
  width: 60%;
  margin: auto;
}
.map {
  width: 450px;
  height: 305px;
}
.intro {
  margin: 13px auto 28px 45px;
}
.intro_item {
  margin: 5px 15px 15px auto;
}
</style>