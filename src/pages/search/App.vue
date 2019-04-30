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
        <p v-if="mohuSearch" class="mohuSearch">对不起，找不到您搜索的内容，我猜您是想搜以下内容</p>
        <searchBox-vue @jumpDetail="jumpDetail" v-for="(item,index) in searchData" :key="index"
        :type="1" :searchData="item" v-show="hasData"></searchBox-vue>
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
        mohuSearch:false,
      }
    },
    components:{
      headerVue,
      footerVue,
      searchBoxVue,
      pageVue,
    },
    methods:{
      onKeyUp(){
        let ev = window.event;
        if(ev.keyCode === 13){
          this.onSearch();
        }
      },
      //图片加载超时提示
      open6() {
        this.once = true;
        this.$notify.error({
          title: '错误',
          message: '（╯#-皿-)╯~~╧═╧ 服务器故障，图片加载失败'
        });
      },
      //跳转至详情页
      jumpDetail(dataObject){
        window.sessionStorage.setItem('animeDetail',JSON.stringify(dataObject));
        window.location.href = URL.animeDetail;
      },
      //查询按钮按下事件
      findTag(item){
        //设置名称
        this.itemNow = item;
        //重置总页码
        this.pages = 0;
        //重置firstIn
        this.firstIn = true;
        //重置当前页码
        this.currentPage = 1;
        //调用标签查询函数
        this.getAnimeFromTag();
      },
      onSearch(){
        let name = this.nameMsg;
        if(name.replace(/\s/g,'') === ''){
          this.showErrorMsg = true;
          setTimeout(()=>{
            this.showErrorMsg = false;
          },1500)
          return
        }
        //调用接口查询数据，根据名称查询，若查询结果为空，启用模糊查询，利用拼音转译查询相似拼音的数据
        this.$http.get('http://127.0.0.1:9876/getAnime?name='+this.nameMsg).then(res=>{
          if(JSON.parse(res.bodyText).length === 0){
            let str = [];
            pinyin(this.nameMsg).forEach(data => {
              str.push(data[0].charAt(0))
            });
            this.$http.get('http://127.0.0.1:9876/getsmallname?regname='+str.join('')).then(res=>{
              this.mohuSearch = true;
              this.searchData = JSON.parse(res.bodyText);
              this.hasData = true;
            })
          }else{
            this.mohuSearch = false;
            this.searchData = JSON.parse(res.bodyText);
            this.hasData = true;
          }
        })
      },
      //根据标签查询
      getAnimeFromTag(){
        //若当前页码为0
        if(this.pages === 0){
          //第一次进入，加载总页码加载第一页，预加载第二页数据
          if(this.firstIn){
            this.firstIn=false;
            this.requestAnime(0);
            this.requestAnime(1);
            this.getAnimePage();
          }else{
            this.requestAnime(3);
            this.requestAnime(4);
          }
        //当前页码不为0
        }else{
          //若第一次按下且按下为第二页即pagesPre为0.下一页为2，或点击下一页，把预请求数据直接填载如当前数据，继续预加载下一页数据
          if(this.pagesPre === 0 && this.pages === 2 || this.pages-this.pagesPre === 1){
            this.requestAnime(2);
          }else{
            //加载点击页数据预加载下一页数据
            this.requestAnime(3);
            this.requestAnime(4);
          }
        }
      },
      //调用接口获取总数计算总页码函数
      getAnimePage(){
        this.$http.get('http://127.0.0.1:9876/getCount?tag='+this.itemNow).then(res=>{
          let allPage = JSON.parse(res.bodyText).page/20
          this.allPages = parseInt(allPage+1);
        })
      },
      //页码改变，改变当页码，上一次页码，并调取函数获取数据
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
        //判断类型，若为0；则是第一次加载
        //         若为1，则是预加载
        //         若为2，则是点击下一页是的预加载
        //         若为3，则是点击其他页面时的加载
        //         若为4，非第一次请求的加载
        if(type===0){
          trunPage = this.pages;
        }else if(type===1){
          trunPage = this.pages + 1;
        }else if(type===2){
          trunPage = this.pages;
          if(this.searchDataNext === null){
            setTimeout(() => {
              this.searchData = this.searchDataNext;
            }, 500);
          }
        }else if(type===3){
          trunPage = this.pages-1;
        }else if(type===4){
          trunPage = this.pages
        }
        //请求数据
        this.$http.get('http://127.0.0.1:9876/findTag?tag='+this.itemNow+'&skip='+trunPage).then(res=>{
          let tempObject = JSON.parse(res.bodyText);
          let iNum = 0;
          let _this = this;
          //设置定时器，若超时，则利用空代替图片，并提示错误信息
          setTimeout(()=>{
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
          },5000)
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

                  }
                  _this.hasData = true;
                }
              }
            })(i)
          }
        })
      }
    },
    created() {
      window.addEventListener('keyup',this.onKeyUp)
      //获取上一个页面传递的数据，并删除
      let searchName = window.sessionStorage.getItem('animeSearch');
      window.sessionStorage.removeItem('animeSearch');
      let searchTag = window.sessionStorage.getItem('findTagAnime');
      window.sessionStorage.removeItem('findTagAnime');
      //判断是否存在上一个页面传来的数据，若存在，直接显示
      if(!!searchName){
        this.searchData = JSON.parse(searchName)
        this.mohuSearch = window.sessionStorage.getItem('mohuSearch')==='1'?false:true;
        window.sessionStorage.removeItem('mohuSearch')
        this.hasData = true;
      }
      if(!!searchTag){
        this.itemNow = searchTag;
        this.getAnimeFromTag()
      }
    },
    computed: {
      //计算是否需要显示总页码，当页码为1或者页面为0时，不需显示总页码
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
  .mohuSearch{
    text-align: center;
    color: #F93232;
  }
</style>
