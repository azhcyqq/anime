<template>
  <div id="app">
    <header-vue @searchClick='search'></header-vue>

    <slider-vue @jumpImg="imgGo"></slider-vue>
    <uiList-vue @imgGo="imgGo" :type="0"></uiList-vue>
    <div class="content-box">
      <div class="linkBox-box">
        <linkbox-vue @goDetail="imgGo" class="linkBox" v-for="(item,index) in animeData" :key="index" :type="0" :boxData="item"></linkbox-vue>
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
import pinyin from 'pinyin'
export default {
  components:{
    headerVue,
    uiListVue,
    footerVue,
    linkboxVue,
    sliderVue,
  },
  data(){
    return{
      name: 'App',
      animeData:[],
      imgData:null,
    }
  },
  created(){
    this.$http.get('http://127.0.0.1:9876/getlunbo?num=6').then(res=>{
      this.animeData = JSON.parse(res.bodyText)
    });
  },
  methods:{
    search(searchData){
      console.log(searchData)
      window.localStorage.setItem('animeSearch',JSON.stringify(searchData))
      // console.log(JSON.parse(window.localStorage.getItem('animeSearch')))
      window.location.href = 'http://127.0.0.1:8080/search.html';
    },
    imgGo(data){
      window.localStorage.setItem('animeDetail',JSON.stringify(data));
      window.location.href = 'http://127.0.0.1:8080/animeDetail.html'
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
  width: 1050px;
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
  margin: 30px;
}
.content-box{
  position: relative;
  width: 100%;
  margin: 0 auto 110px;
  background-color: rgba(64,158,255,0.7)
}
.content-box::after{
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
</style>
