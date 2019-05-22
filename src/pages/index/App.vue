<template>
  <div id="app">
    <header-vue @searchClick='search'></header-vue>
    <slider-vue @jumpImg="imgGo"></slider-vue>
    <div class="weekBox">
      <uiList-vue class="week-box2" @imgGo="imgGo" :type="0"></uiList-vue>
      <!-- <uiList-vue class="week-box1" @imgGo="imgGo" :type="1"></uiList-vue> -->
    </div>
    <div class="content-box">
      <p class="content-box-p">热门推荐<span class="content-box-span" @click="goToRank">点击查看排行榜</span></p>
      <div class="linkBox-box">
        <linkbox-vue @goDetail="imgGo" class="linkBox" v-for="(item,index) in animeData" :key="index" :type="0" :boxData="item"></linkbox-vue>
      </div>
    </div>
    <div class="backgroundDiv1">
      <searchBox-vue :position="'middle'" :showRecommend="false" :type="0" @tagFind="findTag"></searchBox-vue>
    </div>
    <div class="line"></div>
    <div class="content-box">
      <p class="content-box-p">冒险(*•̀ㅂ•́)و<span class="content-box-span" @click="goToTag(1)">点击查看更多</span></p>
      <div class="linkBox-box">
        <linkbox-vue @goDetail="imgGo" class="linkBox" v-for="(item,index) in hotTag1" :key="index" :type="0" :boxData="item"></linkbox-vue>
      </div>
    </div>
    <div class="line"></div>
    <div class="content-box">
      <p class="content-box-p">奇幻๑乛◡乛๑<span class="content-box-span" @click="goToTag(2)">点击查看更多</span></p>
      <div class="linkBox-box">
        <linkbox-vue @goDetail="imgGo" class="linkBox" v-for="(item,index) in hotTag2" :key="index" :type="0" :boxData="item"></linkbox-vue>
      </div>
    </div>
    <div class="backgroundDiv2">
      <searchBox-vue :position="'middle'" :showRecommend="true" :type="2" @tagFind="findAZ"></searchBox-vue>
    </div>
    <div class="content-box">
      <p class="content-box-p">搞笑（●´∀｀）♪<span class="content-box-span" @click="goToTag(3)">点击查看更多</span></p>
      <div class="linkBox-box">
        <linkbox-vue @goDetail="imgGo" class="linkBox" v-for="(item,index) in hotTag3" :key="index" :type="0" :boxData="item"></linkbox-vue>
      </div>
    </div>
    <banner-vue :imgPosition="'banner-left'" :imgSrc="jingdong"></banner-vue>
    <banner-vue :imgPosition="'banner-right'" :imgSrc="tianmao"></banner-vue>
    <footer-vue></footer-vue>
  </div>
</template>

<script>
import headerVue from '@com/header'
import uiListVue from '@com/uiList'
import footerVue from '@com/footer'
import linkboxVue from '@com/Linkbox'
import sliderVue from '@com/slider'
import searchBoxVue from '@com/searchBox'
import pinyin from 'pinyin'
import URL from '@mock/url.json'
import bannerVue from '@com/banner'
export default {
  components:{
    headerVue,
    uiListVue,
    footerVue,
    linkboxVue,
    sliderVue,
    searchBoxVue,
    bannerVue,
  },
  data(){
    return{
      name: 'App',
      animeData:[],
      imgData:null,
      hotTag1:null,
      hotTag2:null,
      hotTag3:null,
      tianmao:'',
      jingdong:'',
    }
  },
  created(){
    //不清楚是否需要加此功能（滚动条滚动事件）
    // window.addEventListener('scroll',this.onScrollChange)
    //获取轮播图数据并展示
    this.$http.get('http://127.0.0.1:9876/getlunbo?num=10').then(res=>{
      this.animeData = JSON.parse(res.bodyText)
    });
    //获取热门标签的数据
    this.getHotTag();
    this.tianmao = require('@a/tianmao.jpg');
    this.jingdong = require("@a/jingdong.jpg");
  },
  methods:{
    //跳转至排行榜页面
    goToRank(){
      window.location.href = URL.rank;
    },
    //点击搜索查询
    search(searchData){
      console.log(searchData)
      window.sessionStorage.setItem('animeSearch',JSON.stringify(searchData))
      window.location.href = URL.search;
    },
    //点击图片或文字跳转至对应详情页面
    imgGo(data){
      window.sessionStorage.setItem('animeDetail',JSON.stringify(data));
      window.location.href = URL.animeDetail;
    },
    //获取热门标签数据
    getHotTag(){
      this.$http.get('http://127.0.0.1:9876/gethottag?tag=冒险').then(res=>{
        this.hotTag1 = JSON.parse(res.bodyText)
      })
      this.$http.get('http://127.0.0.1:9876/gethottag?tag=奇幻').then(res=>{
        this.hotTag2 = JSON.parse(res.bodyText)
      })
      this.$http.get('http://127.0.0.1:9876/gethottag?tag=搞笑').then(res=>{
        this.hotTag3 = JSON.parse(res.bodyText)
      })
    },
    //根据标签跳转至搜索页面
    goToTag(type){
      let tag = '';
      if(type===1){
        tag = '冒险'
      }
      else if(type===2){
        tag = '奇幻'
      }
      else if(type===3){
        tag = '搞笑'
      }
      window.sessionStorage.setItem('findTagAnime',tag);
      window.location.href = URL.search
    },
    //根据标签查询跳转至搜索页面
    findTag(tag){
      window.sessionStorage.setItem('findTagAnime',tag);
      window.location.href = URL.search
    },
    findAZ(index){
      window.sessionStorage.setItem('findAZ',String.fromCharCode('a'.charCodeAt()+index));
      window.location.href = URL.search
    }
  }
}
</script>

<style>
*{
  margin: 0;
  padding: 0;
}
.linkBox-box{
  width: 1250px;
  height: 100%;
  position: relative;
  z-index: 99;
  margin: 0 auto;
}
.linkBox-box::after{
  display: block;
  content: '';
  clear: both;
}
.linkBox{
  float:left;
  margin: 25px 35px;
}
.line{
  height: 1px;
  background: #ccc;
}
.content-box{
  position: relative;
  width: 100%;
  margin: 0 auto 20px;
}
.content-box-p{
  text-indent: 35px;
  font-size: 25px;
  margin-top: 5px;
  cursor: default;
}
.content-box-span{
  font-size: 16px;
  cursor: pointer;
}
.content-box-span:hover{
  color:rgb(233, 95, 92);
}
.backgroundDiv1{
  background: rgba(57, 142, 186, 0.6);
  position: relative;
}
.backgroundDiv2{
  background: rgba(57, 142, 186, 0.6);
  position: relative;
}
.backgroundDiv2::after,.backgroundDiv1::after{
  content: '';
  display: block;
  width: 100%;
  height: 100%;
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  opacity: 0.5;
}
.backgroundDiv2::after{
  background: url('~@a/bg2.jpeg');
  background-size: 100% 100%;
  background-attachment: fixed;
}
.backgroundDiv1::after{
  background: url('~@a/bg1.jpg');
  background-size: 100% 100%;
  background-attachment: fixed;
}
.weekBox{
  width: 80%;
  margin: 0 auto;
}
.weekBox::after{
  content: '';
  display: block;
  clear: both;
}
</style>
