<template>
  <div style="display: flex; flex-direction: column; min-height: 100vh;">
    <div class="not-bottom">
      <head-nav />
      <div class="img">
        <img src="~@/assets/images/logo.png" alt="">
      </div>
      <div class="middle">
        <el-form :inline="true" ref="form">
            <el-form-item>
              <el-input v-model="search" placeholder="请输入要查询的IP地址" @keyup.enter.native="searchButton()"></el-input>
            </el-form-item>
            <el-form-item>
              <el-button type="primary" @click="searchButton()">Search</el-button>
            </el-form-item>
          </el-form>
      </div>
      <div class="tabs">
        <ul class="list">
          <li v-for="(item, index) in selected" :key="index" @click="select(index)">
            <a href="javaScript:void(0)" :class="{active:isSelected === index}">{{item}}</a>
          </li>
        </ul>
      </div>
      <div class="content">
        <ul class="list">
          <li v-for="(item, index) in selected" :key="index">
            <div class="content-container" v-show="isSelected === 0"></div>
          </li>
        </ul>
        <!-- <div class="content-container clearfix"  v-show="isSelected === 0">
          <div class="left">
            <div id="map" class="map"></div>
          </div>
          <div class="right">
            <div class="introduction">
              <div class="title"></div>
            </div>
          </div>
        </div>
        <div class="content-container clearfix" v-show="isSelected === 1">
        </div>
        <div class="content-container" v-show="isSelected === 2">
        </div>
        <div class="content-container" v-show="isSelected === 3">
        </div>
        <div class="content-container" v-show="isSelected === 4">
        </div>
        <div class="content-container" v-show="isSelected === 5">
        </div>
        <div class="content-container" v-show="isSelected === 6">
        </div>
        <div class="content-container clearfix" v-show="isSelected === 7">
        </div> -->
      </div>
    </div>
    <div class="bottom">
      <span>@2021 穹眼安全 京ICP备18044233号-2</span>
    </div>
  </div>
</template>

<script>
import headNav from "@/components/HeadNav";
import {getLabelData} from "../../api/apiData";

export default {
  name: 'homeSearch',
  components:{
    headNav,
  },
  data() {
    return {
      search: null,
      selected: '',
      isSelected: 0,
      drawMap_byIP_data: null,
      mapData_dot: null,
    }
  },
  mounted() {
    this.search = this.$route.params.searchContent;
    let pattern = new RegExp(/^s*((([0-9A-Fa-f]{1,4}:){7}([0-9A-Fa-f]{1,4}|:))|(([0-9A-Fa-f]{1,4}:){6}(:[0-9A-Fa-f]{1,4}|((25[0-5]|2[0-4]d|1dd|[1-9]?d)(.(25[0-5]|2[0-4]d|1dd|[1-9]?d)){3})|:))|(([0-9A-Fa-f]{1,4}:){5}(((:[0-9A-Fa-f]{1,4}){1,2})|:((25[0-5]|2[0-4]d|1dd|[1-9]?d)(.(25[0-5]|2[0-4]d|1dd|[1-9]?d)){3})|:))|(([0-9A-Fa-f]{1,4}:){4}(((:[0-9A-Fa-f]{1,4}){1,3})|((:[0-9A-Fa-f]{1,4})?:((25[0-5]|2[0-4]d|1dd|[1-9]?d)(.(25[0-5]|2[0-4]d|1dd|[1-9]?d)){3}))|:))|(([0-9A-Fa-f]{1,4}:){3}(((:[0-9A-Fa-f]{1,4}){1,4})|((:[0-9A-Fa-f]{1,4}){0,2}:((25[0-5]|2[0-4]d|1dd|[1-9]?d)(.(25[0-5]|2[0-4]d|1dd|[1-9]?d)){3}))|:))|(([0-9A-Fa-f]{1,4}:){2}(((:[0-9A-Fa-f]{1,4}){1,5})|((:[0-9A-Fa-f]{1,4}){0,3}:((25[0-5]|2[0-4]d|1dd|[1-9]?d)(.(25[0-5]|2[0-4]d|1dd|[1-9]?d)){3}))|:))|(([0-9A-Fa-f]{1,4}:){1}(((:[0-9A-Fa-f]{1,4}){1,6})|((:[0-9A-Fa-f]{1,4}){0,4}:((25[0-5]|2[0-4]d|1dd|[1-9]?d)(.(25[0-5]|2[0-4]d|1dd|[1-9]?d)){3}))|:))|(:(((:[0-9A-Fa-f]{1,4}){1,7})|((:[0-9A-Fa-f]{1,4}){0,5}:((25[0-5]|2[0-4]d|1dd|[1-9]?d)(.(25[0-5]|2[0-4]d|1dd|[1-9]?d)){3}))|:)))(%.+)?s*$/)
    if(pattern.test(this.search)) {
      this.getData();
    }
  },
  methods: {
    select(index) {
      this.isSelected = index
    },
    searchButton() {
      this.getData();
    },
    getData() {
      getLabelData(this.search).then(judgeResult => {
        console.log('结果是啥：', judgeResult)
        // 先把res转成有序
        this.selected = judgeResult['key'];
        // 不能这样写
        this.drawMap_byIP_data = judgeResult['value']['地理位置']
        let dot_dic ={};
        dot_dic.country = this.drawMap_byIP_data.country;
        dot_dic.coord = [this.drawMap_byIP_data.longitude, this.drawMap_byIP_data.latitude];
        this.mapData_dot = [];
        this.mapData_dot.push(dot_dic)
        this.drawMap();
      })
    },
    drawMap() {
      let map = this.$echarts.init(document.getElementById("map"));
      map.setOption({
        tooltip: {
            trigger: 'item',
            triggerOn: 'mousemove'
          },
          series: [
            {
              type: "map",
              name: "test",
              coordinateSystem:'geo',
              label: {
                show: false,
                position: "top",
                margin: 5
              },
              mapType: "world",
              roam: false,
              zoom: 1.15,
              // center: [115.97, 29.71],
              // 去除各个国家上的小红点
              showLegendSymbol: false,
              markPoint: {//动态标记
                large: true,//这个选项，悬浮自动失效
                symbolSize: 9,
                symbol: 'circle',
                itemStyle: {
                  normal: {
                    shadowBlur: 2,
                    shadowColor: 'rgba(37, 140, 249, 0.8)',
                    color: '#f00'
                  }
                },
                data: this.mapData,
              }
            }
          ],
      });
    }
  }
}
</script>

<style lang="scss" scoped>
@import "~@/assets/css/reset.css";
.not-bottom {
  flex: 1;
}
.img{
  img {
    position: relative;
    width: 18%;
  }
  margin-top: 3.8%;
  text-align: center;
}
.middle{
  width: 600px;
  margin: 20px auto;
  .el-form-item {
    margin: 0;
  }
  .el-form-item:first-of-type {
    width: 87%;
  }
  .el-form-item:last-of-type {
    width: 13%;
  }
  .el-form-item /deep/ .el-form-item__content {
    line-height: 100%;
    display: inline;
  }
  .el-input /deep/ .el-input__inner {
    height: 37px;
    border-radius: 6px 0 0 6px;
    border: 2px solid #d4d4d4;
    border-right: 1px solid transparent;
  }
  .el-input /deep/ .el-input__inner:focus {
    border-color: #b6b6b6;
  }
  .el-form-item /deep/ .el-button--primary {
    font-size: 15px;
    background-color: #b6b6b6;
    border-color: #cecece;
  }
  .el-button {
    height: 37px;
    width: 80px;
    border-radius: 0 6px 6px 0;
    display: flex;
    justify-content: center;
    align-items: center;
  }
}
/* tab选项卡的样式 */
.tabs{
  display: flex;
  justify-content: center;
  width: 880px;
  height: 36px;
  margin: 35px auto 0 auto;
  /*border-bottom: 1px solid #000;*/
  background-color: #f5f5f5;
  .list{
    display: flex;
    justify-content: space-around;
    width: 100%;
    position: relative;
    li:first-child{
      margin-left: 50px;
    }
    li:last-child{
      margin-right: 50px;
    }
    li{
      height: 30px;
      a{
        margin-top: 5px;
        padding: 3px 6px 8px 6px;
        height: 20px;
        color: #009a61;
        font-size: 18px;
      }
    }
  }
}
/* 内容样式 */
.content{
  width: 878px;
  height: 380px;
  border: 1px solid rgb(194, 194, 194);
  margin: 0 auto;
  background-color: #fff;
  .content-container{
    .left {
      width: 450px;
      height: 380px;
      float: left;
      .map {
        width: 450px;
        height: 380px;
      }
    }
    .right {
      width: 350px;
      height: 380px;
      float: right;
      .introduction {

      }
    }
  }
  /* 清浮动 */
  .clearfix:after{
    content: "" ;
    display: block;
    height: 0;
    clear: both;
    visibility: hidden;
  }
}
/* 选中tab时的样式 */
.active{
  background: #fff;
  border: 1.3px solid rgb(218, 218, 218);
  border-bottom: 1px solid transparent;
}
.bottom {
  background: rgb(245, 245, 245);
}
</style>
