<template>
  <div>
    <header-vue :searchShow="false"></header-vue>
    <searchBox-vue :showRank="true" v-for="(item,index) in rankData" :key="index"
    :rankNum="index" :type="1" :searchData="item" @jumpDetail="jumpDetail"></searchBox-vue>
    <footer-vue></footer-vue>
  </div>
</template>
<script>
import searchBoxVue from '@com/searchBox'
import headerVue from '@com/header'
import footerVue from '@com/footer'
import URL from '@mock/url.json'
export default {
  components:{
    searchBoxVue,
    footerVue,
    headerVue,
  },
  data(){
    return{
      rankData:null,
    }
  },
  methods:{
    init(){
      this.$http.get('http://127.0.0.1:9876/getRank').then(res=>{
        this.rankData = JSON.parse(res.bodyText);
      })
    },
    jumpDetail(dataObject){
      window.sessionStorage.setItem('animeDetail',JSON.stringify(dataObject));
      window.location.href = URL.animeDetail;
    }
  },
  created(){
    this.init();
  }
}
</script>
<style>

</style>
