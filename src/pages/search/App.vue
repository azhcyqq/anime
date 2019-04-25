<template>
  <div id="app">
      <header-vue :searchShow="false"></header-vue>
      <div class="search-result">
        <div class="search">
          <el-input
            placeholder="请输入内容"
            v-model="nameMsg"
            clearable>
          </el-input>
          <el-button class="searchBtn" icon="el-icon-search" circle @click="onSearch"></el-button>
        </div>
        <searchBox-vue :type="0" @tagFind="findTag"></searchBox-vue>
        <searchBox-vue v-for="(item,index) in searchData" :key="index" :type="1" :searchData="item"></searchBox-vue>
        <page-vue :showPage="showPage" @currentChange="pageChange" :maxLen="allPages"></page-vue>
        <!-- <page-vue></page-vue> -->
      </div>
      <footer-vue></footer-vue>
  </div>
</template>
<script>
import headerVue from '@com/header'
import footerVue from '@com/footer'
import searchBoxVue from '@com/searchBox'
import pageVue from '@com/page'
export default {
    data(){
      return{
        name: 'App',
        nameMsg:'',
        pages:0,
        allPages:0,
        searchData:null,
        itemNow:'',
      }
    },
    components:{
      headerVue,
      footerVue,
      searchBoxVue,
      pageVue,
    },
    methods:{
      findTag(item){
        this.itemNow = item;
        this.getAnimeFromTag();
        this.getAnimePage();
      },
      onSearch(){
        this.$http.get('http://127.0.0.1:9876/getAnime?name='+this.nameMsg).then(res=>{
          this.searchData = JSON.parse(res.bodyText);
        })
      },
      getAnimeFromTag(){
        this.pages = this.pages===0?this.pages:this.pages-1;
        console.log(this.pages)
        this.$http.get('http://127.0.0.1:9876/findTag?tag='+this.itemNow+'&skip='+this.pages).then(res=>{
          this.searchData = JSON.parse(res.bodyText);
        })
      },
      getAnimePage(){
        this.$http.get('http://127.0.0.1:9876/getCount?tag='+this.itemNow).then(res=>{
          let allPage = JSON.parse(res.bodyText).page/20
          this.allPages = parseInt(allPage+1);
        })
      },
      pageChange(page){
        this.pages = page;
        this.getAnimeFromTag();
      }
    },
    created() {
      let searchName = window.localStorage.getItem('animeSearch');
      window.localStorage.removeItem('animeSearch');
      let searchTag = window.localStorage.getItem('findTagAnime');
      window.localStorage.removeItem('findTagAnime');
      if(!!searchName){
        this.searchData = JSON.parse(searchName)
      }
      if(!!searchTag){
        this.itemNow = searchTag;
        this.getAnimeFromTag()
      }
    },
    computed: {
      showPage(){
        if(this.allPages===1 || this.allPages === 0){
          return false;
        }
        return true;
      }
    },
}
</script>
<style>
  .search-result{
    margin: 20px 0 110px 150px;
  }
  .search-result .search{
    width: 50%;
    height: 35px;
    position: relative;
  }
  .search-result .search .searchBtn{
    position: absolute;
    left: 100%;
    top: 0%;
  }
</style>
