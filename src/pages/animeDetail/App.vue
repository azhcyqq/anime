<template>
  <div>
    <header-vue :searchShow="false"></header-vue>
    <div class="anime">
      <div class="anime-detail">
        <div class="anime-detail-img">
          <img :src="searchData.img" >
        </div>
        <div class="anime-detail-intro">
          <div>
            <h2>{{searchData.name}}</h2>
            <span>地区：日本</span>
            <span>年份：{{year}}</span>
            <span>标签：<a class="canClick" @click="jumpTo(item)" :key="index" v-for="(item,index) in searchData.tag">{{item}}</a></span>
            <span></span>
            <span class="introduce">简介：{{searchData.introduce}}</span>
          </div>
        </div>
      </div>
      <div class="anime-play">
        <animeplay-vue @goPlay="playGo" :dataObject="searchData"></animeplay-vue>
      </div>
      <div class="linkBox-box">
        <el-carousel height="250px" :autoplay="false">
          <el-carousel-item v-for="(item,index) in 3" :key="index" >
            <div>
              <img class="boxImg canClick" @click="imgGo(index,ind,hotSuggest)" v-for="(it,ind) in 5"
              :key="ind" :src="hotSuggest[5*index+ind].img" :alt="hotSuggest[5*index+ind].name">
            </div>
          </el-carousel-item>
        </el-carousel>
      </div>
    </div>
    <footer-vue></footer-vue>
  </div>
</template>

<script>
import headerVue from '@com/header'
import footerVue from '@com/footer'
import searchBoxVue from '@com/searchBox'
import pageVue from '@com/page'
import animeplayVue from '@com/animeplay'
import linkboxVue from '@com/Linkbox'
import URL from '@mock/url.json'
export default {
    data(){
      return{
        searchData:null,
        year:0,
        hotSuggest:null,
      }
    },
    components: {
      headerVue,
      footerVue,
      searchBoxVue,
      pageVue,
      animeplayVue,
      linkboxVue,
    },
    created() {
      this.searchData = JSON.parse(window.sessionStorage.getItem('animeDetail'));
      this.year = Math.round(Math.random()*10+2000);
      this.$http.get('http://127.0.0.1:9876/gethot?small=1&page='+Math.round( (Math.random()*10)+10 ) ).then(res=>{
        this.hotSuggest = JSON.parse(res.bodyText)
      })
    },
    methods: {
      jumpTo(tag){
        window.sessionStorage.setItem('findTagAnime',tag)
        window.location.href = URL.search;
      },
      imgGo(index,ind,hotSuggest){
        window.sessionStorage.setItem('animeDetail',JSON.stringify(hotSuggest[index*5+ind]))
        window.location.reload();
      },
      playGo(animeData,index){
        animeData.playNow = index+1;
        window.sessionStorage.setItem('playData',JSON.stringify(animeData))
        window.location.href = URL.animeplay
      }
    }
}
</script>
<style>
  *{
    margin: 0;
    padding: 0;
  }
  .anime{
    margin:20px;
    margin-bottom: 110px;
  }
  .anime-detail{
    display: inline-block;
    position: relative;
    left: 50%;
    transform: translate(-50%)
  }
  .anime .anime-detail .anime-detail-img img{
    width:220px;
  }
  .anime .anime-detail .anime-detail-img::after,.anime-detail::after{
    display:block;
    content:'';
    clear:both;
  }
  .anime .anime-detail .anime-detail-intro,.anime .anime-detail .anime-detail-img{
    margin-left:30px;
    /* float:left; */
    display:inline-block;
  }

  .anime .anime-detail .anime-detail-intro{
    width:480px;
  }
  .anime .anime-detail .anime-detail-intro h2{
    margin:0;
    color:#E6A23C
  }
  .anime .anime-detail .anime-detail-intro span{
    display:inline-block;
    width:50%;
    height:30px;
    line-height:30px;
    /* float:left; */
    margin-top:5px;
  }
  .anime .anime-detail .anime-detail-intro span a{
    margin-left:5px;
  }
  .anime .anime-detail .anime-detail-intro .introduce{
    width:100%
  }
  .canClick{
    cursor:pointer;
    color: rgba(213, 21, 21,0.8);
  }
  .canClick:hover{
    transition: 0.2s;
    color: red;
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
  .boxImg{
    width: 180px;
    height: 250px;
    margin: 0 10px;
  }
  .el-carousel{
    width: 1000px;
    margin: 0 auto;
  }
  .anime-play{
    margin: 0 auto;
  }
  .canClick{
    cursor: pointer;
  }
</style>
