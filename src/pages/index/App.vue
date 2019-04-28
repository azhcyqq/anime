<template>
  <div id="app">
    <header-vue @searchClick='search'></header-vue>

    <slider-vue @jumpImg="imgGo"></slider-vue>
    <div class="weekBox">
      <uiList-vue class="week-box1" @imgGo="imgGo" :type="1"></uiList-vue>
      <uiList-vue class="week-box2" @imgGo="imgGo" :type="0"></uiList-vue>
    </div>
    <div class="content-box">
      <p class="content-box-p"><span>热门推荐</span></p>
      <div class="linkBox-box">
        <linkbox-vue @goDetail="imgGo" class="linkBox" v-for="(item,index) in animeData" :key="index" :type="0" :boxData="item"></linkbox-vue>
      </div>
    </div>
    <div class="backgroundDiv">
      <searchBox-vue :position="'middle'" :showRecommend="false" :type="0" @tagFind="findTag"></searchBox-vue>
    </div>
    <div class="line"></div>
    <div class="content-box">
      <p class="content-box-p">冒险๑乛◡乛๑<span class="content-box-span" @click="goToTag(1)">点击查看更多</span></p>
      <div class="linkBox-box">
        <linkbox-vue @goDetail="imgGo" class="linkBox" v-for="(item,index) in hotTag1" :key="index" :type="0" :boxData="item"></linkbox-vue>
      </div>
    </div>
    <div class="line"></div>
    <div class="content-box">
      <p class="content-box-p">奇幻(*•̀ㅂ•́)و<span class="content-box-span" @click="goToTag(2)">点击查看更多</span></p>
      <div class="linkBox-box">
        <linkbox-vue @goDetail="imgGo" class="linkBox" v-for="(item,index) in hotTag2" :key="index" :type="0" :boxData="item"></linkbox-vue>
      </div>
    </div>
    <div class="line"></div>
    <div class="content-box">
      <p class="content-box-p">搞笑（●´∀｀）♪<span class="content-box-span" @click="goToTag(3)">点击查看更多</span></p>
      <div class="linkBox-box">
        <linkbox-vue @goDetail="imgGo" class="linkBox" v-for="(item,index) in hotTag3" :key="index" :type="0" :boxData="item"></linkbox-vue>
      </div>
    </div>
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
export default {
  components:{
    headerVue,
    uiListVue,
    footerVue,
    linkboxVue,
    sliderVue,
    searchBoxVue,
  },
  data(){
    return{
      name: 'App',
      animeData:[],
      imgData:null,
      hotTag1:null,
      hotTag2:null,
      hotTag3:null,
    }
  },
  created(){
    this.$http.get('http://127.0.0.1:9876/getlunbo?num=10').then(res=>{
      this.animeData = JSON.parse(res.bodyText)
    });
    this.getHotTag();
  },
  methods:{
    search(searchData){
      window.sessionStorage.setItem('animeSearch',JSON.stringify(searchData))
      window.location.href = 'http://127.0.0.1:8080/search.html';
    },
    imgGo(data){
      window.sessionStorage.setItem('animeDetail',JSON.stringify(data));
      window.location.href = 'http://127.0.0.1:8080/animeDetail.html'
    },
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
      window.location.href = 'http://127.0.0.1:8080/search.html'
    },
    findTag(tag){
      window.sessionStorage.setItem('findTagAnime',tag);
      window.location.href = 'http://127.0.0.1:8080/search.html'
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
.backgroundDiv{
  background: rgba(57, 142, 186, 0.6);
  position: relative;
}
.backgroundDiv::after{
  content: '';
  display: block;
  width: 100%;
  height: 100%;
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  background: url('~@a/bg1.jpg');
  background-size: 100% 100%;
  background-attachment: fixed;
  opacity: 0.5;
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
.week-box1{
  float: left;
}
.week-box2{
  float: right;
}
</style>
