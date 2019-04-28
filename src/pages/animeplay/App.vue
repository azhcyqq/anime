<template>
  <div>
    <header-vue :searchShow="false"></header-vue>
    <banner-vue :imgPosition="'banner-left'"></banner-vue>
    <banner-vue :imgPosition="'banner-right'"></banner-vue>
    <frameplay-vue @fleshPlay="fleshPlay" :playData="playData"></frameplay-vue>
    <div class="linkBox-box">
      <el-carousel height="250px">
        <el-carousel-item v-for="(item,index) in 3" :key="index" >
          <img class="boxImg canClick" @click="imgGo(index,ind,hotSuggest)" v-for="(it,ind) in 3"
           :key="ind" :src="hotSuggest[3*index+ind].img" :alt="hotSuggest[3*index+ind].name">
        </el-carousel-item>
      </el-carousel>
    </div>
    <footer-vue></footer-vue>
  </div>
</template>
<script>
import bannerVue from '@com/banner'
import frameplayVue from '@com/frameplay'
import headerVue from '@com/header'
import footerVue from '@com/footer'
export default {
  data() {
    return {
      playData:null,
      hotSuggest:null
    }
  },
  components:{
    bannerVue,
    frameplayVue,
    headerVue,
    footerVue,
  },
  created(){
    this.playData = JSON.parse(window.sessionStorage.getItem('playData'))
    if(this.playData.tag === []){
      this.$http.get('http://127.0.0.1:9876/gethot?small=1&page='+Math.round( (Math.random()*10)+10 ) ).then(res=>{
          this.hotSuggest = JSON.parse(res.bodyText)
        })
    }else{
      console.log(this.playData.tag[0])
      let randomTag = this.playData.tag[parseInt(Math.random()*this.playData.tag.length)]
      console.log(this.playData.tag.length)
      this.$http.get('http://127.0.0.1:9876/findAlike?tag='+randomTag).then(res=>{
        this.hotSuggest = JSON.parse(res.bodyText)
      })
    }
  },
  methods:{
    fleshPlay(playData){
      this.playData = playData;
      window.sessionStorage.setItem('playData',JSON.stringify(this.playData))
      window.location.reload();
    },
    imgGo(index,ind,hotSuggest){
      window.sessionStorage.setItem('animeDetail',JSON.stringify(hotSuggest[index*3+ind]))
      window.location.href='http://127.0.0.1:8080/animeDetail.html'
    }
  }
}
</script>
<style>

  .linkBox-box{
    width: 870px;
    height: 100%;
    position: relative;
    z-index: 99;
    margin: 20px auto 110px;
  }
  .linkBox-box::after{
    display: block;
    content: '';
    clear: both;
  }
  .boxImg{
    width: 250px;
    height: 280px;
    margin: 0 20px;
  }
  .el-carousel{
    width: 870px;
    margin: 0 auto;
  }
</style>
