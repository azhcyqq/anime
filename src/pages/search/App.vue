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
        <searchBox-vue @jumpDetail="jumpDetail" v-for="(item,index) in searchData" :key="index" :type="1" :searchData="item" v-show="hasData"></searchBox-vue>
        <page-vue :currentPage="currentPage" :showPage="showPage"
         @currentChange="pageChange" :maxLen="allPages" v-if="hasData"></page-vue>
      </div>
      <footer-vue></footer-vue>
  </div>
</template>
<script>
import headerVue from '@com/header'
import footerVue from '@com/footer'
import searchBoxVue from '@com/searchBox'
import pageVue from '@com/page'
import pinyin from 'pinyin'
import URL from '@mock/url.json'
export default {
    data(){
      return{
        name: 'App',
        nameMsg:'',
        pages:0,
        pagesPre:0,
        allPages:0,
        searchData:null,
        searchDataNext:null,
        itemNow:'',
        firstIn:true,
        currentPage:1,
        hasData:false,
        once:true,
      }
    },
    components:{
      headerVue,
      footerVue,
      searchBoxVue,
      pageVue,
    },
    methods:{
      open6() {
        this.once = true;
        this.$notify.error({
          title: '错误',
          message: '（╯#-皿-)╯~~╧═╧ 服务器故障，图片加载失败'
        });
      },
      jumpDetail(dataObject){
        window.sessionStorage.setItem('animeDetail',JSON.stringify(dataObject));
        window.location.href = URL.animeDetail;
      },
      findTag(item){
        this.itemNow = item;
        this.pages = 0;
        this.firstIn = true;
        this.currentPage = 1;
        this.getAnimeFromTag();
      },
      onSearch(){
        this.$http.get('http://127.0.0.1:9876/getAnime?name='+this.nameMsg).then(res=>{
          if(JSON.parse(res.bodyText).length === 0){
            let str = [];
            pinyin(this.nameMsg).forEach(data => {
              str.push(data[0].charAt(0))
            });
            this.$http.get('http://127.0.0.1:9876/getsmallname?regname='+str.join('')).then(res=>{
              this.searchData = JSON.parse(res.bodyText);
              this.hasData = true;
            })
          }else{
            this.searchData = JSON.parse(res.bodyText);
            this.hasData = true;
            console.log(JSON.parse(res.bodyText))
          }
        })
      },
      getAnimeFromTag(){
        if(this.pages === 0){
          if(this.firstIn){
            this.firstIn=false;
            this.requestAnime(0);
            this.requestAnime(1);
            this.getAnimePage();
          }else{
            this.requestAnime(3);
            this.requestAnime(4);
          }
        }else{
          if(this.pagesPre === 0 && this.pages === 2 || this.pages-this.pagesPre === 1){
            this.requestAnime(2);
          }else{
            this.requestAnime(3);
            this.requestAnime(4);
          }
        }
      },
      getAnimePage(){
        this.$http.get('http://127.0.0.1:9876/getCount?tag='+this.itemNow).then(res=>{
          let allPage = JSON.parse(res.bodyText).page/20
          this.allPages = parseInt(allPage+1);
        })
      },
      pageChange(page){
        this.pagesPre = this.pages;
        this.pages = page;
        this.currentPage = page
        this.getAnimeFromTag();
      },
      // @type
      //      0第一次请求
      //      1预请求下一页
      //      2点击下一页
      //      3点击其他页（上一页，或者跳过下一页跳转）
      //      4非第一次请求
      requestAnime(type){
        let trunPage =-1;
        if(type===0){
          trunPage = this.pages;
        }else if(type===1){
          trunPage = this.pages + 1;
        }else if(type===2){
          trunPage = this.pages;
          this.searchData = this.searchDataNext;
        }else if(type===3){
          trunPage = this.pages-1;
        }else if(type===4){
          trunPage = this.pages
        }
        this.$http.get('http://127.0.0.1:9876/findTag?tag='+this.itemNow+'&skip='+trunPage).then(res=>{
          let tempObject = JSON.parse(res.bodyText);
          let iNum = 0;
          let _this = this;
          setTimeout(()=>{
            console.log(123123)
            console.log(iNum)
            console.log(tempObject.length)
            if(iNum !== tempObject.length){
              if(this.once){
                this.once = false;
                this.open6();
              }
              for(let i=0;i<tempObject.length;i++){
                tempObject[i].img = '';
                if(type===0||type===3){
                  _this.searchData = tempObject;
                }
                else{
                  _this.searchDataNext = tempObject;
                }
                this.hasData = true;
              }
            }
          },1000)
          for(let i=0;i<tempObject.length;i++){
            (function(){
              let oImg = new Image();
              oImg.src = tempObject[i].img;
              oImg.onload = function(){
                iNum++;
                if(iNum === tempObject.length){
                  if(type===0||type===3){
                    _this.searchData = tempObject;
                  }
                  else{
                    _this.searchDataNext = tempObject;
                  }
                  _this.hasData = true;
                }
              }
              oImg.onerror = function(){
                iNum++;
                if(iNum === tempObject.length){
                  if(type===0||type===3){
                    _this.searchData = tempObject;
                  }
                  else{
                    _this.searchDataNext = tempObject;

                  _this.hasData = true;}
                }
              }
            })(i)
          }
        })
      }
    },
    created() {
      let searchName = window.sessionStorage.getItem('animeSearch');
      window.sessionStorage.removeItem('animeSearch');
      let searchTag = window.sessionStorage.getItem('findTagAnime');
      window.sessionStorage.removeItem('findTagAnime');
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
  .noData{
    text-align: center;
  }
</style>
