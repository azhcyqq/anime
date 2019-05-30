<template>
  <!-- 头部组件 -->
  <div class="main-top" ref="top">
    <div class="userInfo">
      <!-- <img src="@a/user.png" @mouseenter="enter" @mouseleave="leave" alt="" class="canClick">
      <div style="margin-top: 20px; height: 200px;z-index:9999;position:relative"   @mouseenter="detailEnter" @mouseleave="detailLeave">
        <el-collapse-transition>
          <div v-show="show3">
            <div class="transition-box">
              <span v-if="login">{{userMsg.username}}</span>
              <div v-if="login">个人中心</div>
              <div v-if="!login" @click="turnLog('0')">登录</div>
              <div v-if="!login" @click="turnLog('1')">注册</div>
              <div v-if="login">我的收藏</div>
              <div v-if="login">观看记录</div>
            </div>
          </div>
        </el-collapse-transition>
      </div> -->
      <div>
        <ul>
          <li @click="turnIndex">首页</li>
          <li @mouseenter="showfenlei = true" @mouseleave="showfenlei = false">分类
            <ul v-show="showfenlei" class="li_ul">
              <li @click="goSearch(index)" v-for="(item,index) in hotTag" :key="index">{{item}}</li>
            </ul>
          </li>
          <li  @mouseenter="showtuijian = true" @mouseleave="showtuijian = false">番剧推荐
            <ul class="li_ul li_tuijian">
              <li @click="goDetail(index)" v-show="showtuijian" v-for="(item,index) in tuijian" :key="index">{{item.name}}</li>
            </ul>
            <ul class="li_ul li_tuijian">
              <li v-if="login" class="flash" v-show="showtuijian" @click="upDatatuijian" >点击刷新</li>
              <li v-if="!login" class="noCollections" v-show="showtuijian">请先登录</li>
            </ul>
          </li>
          <li  @mouseenter="showcollection = true" @mouseleave="showcollection = false">用户收藏
            <ul v-if="collections.length == 0" class="li_ul">
              <li v-if="login" class="noCollections" v-show="showcollection">无收藏动漫</li>
              <li v-if="!login" class="noCollections" v-show="showcollection">请先登录</li>
            </ul>
            <ul v-if="collections.length>=1" class="li_ul li_tuijian">
              <li @click="goCollections(index)" v-show="showcollection" v-for="(item,index) in collections" :key="index">{{item.name}}</li>
            </ul>
          </li>
          <li  @mouseenter="showhistory = true" @mouseleave="showhistory = false">观看记录
            <ul v-if="history.length >=1" class="li_ul li_tuijian">
              <li v-show="showhistory" v-for="(item,index) in history" :key="index">{{item}}</li>
            </ul>
            <ul v-if="history.length === 0" class="li_ul">
              <li v-if="login" class="noCollections" v-show="showhistory">无观看记录</li>
              <li v-if="!login" class="noCollections" v-show="showhistory">请先登录</li>
            </ul>
          </li>
          <li @click="goUser"  @mouseenter="showuser = true" @mouseleave="showuser = false">个人中心
            <ul class="li_ul">
              <li v-show="showuser" v-if="!login" @click="turnLog('0')">登录</li>
              <li v-show="showuser" v-if="!login" @click="turnLog('1')">注册</li>
              <li v-show="showuser" v-if="login" @click="zhuxiao">注销</li>
            </ul>
          </li>
        </ul>
      </div>
    </div>
    <p @click="turnIndex">守❤护❤世❤界❤上❤最❤好❤的❤坤❤坤</p>
    <div v-if='searchShow' class="searchBox">
      <transition name="fade">
        <span v-if="showErrorMsg" class="dili">输入数据不能为空<i>1</i></span>
      </transition>
      <el-input class="inputMsg"
        placeholder="请输入内容"
        v-model="nameMsg"
        clearable>
      </el-input>
      <el-button icon="el-icon-search" circle @click="onSearch"></el-button>
    </div>
  </div>
</template>
<script>
import Vue from 'vue'
import pinyin from 'pinyin'
import URL from '@mock/url.json'
export default({
  props:{
    searchShow:{
      type:Boolean,
      default:true,
    }
  },
  created() {
    window.addEventListener('keyup',this.onKeyUp)
    window.addEventListener('scroll',()=>{
      if(window.scrollY>5){
        this.$refs.top.style.height = '50px';
      }else{
        this.$refs.top.style.height = '150px';
      }
    })
    this.login = window.sessionStorage.getItem('hasLogin')?true:false;
    this.userMsg = window.sessionStorage.getItem('user')?JSON.parse(window.sessionStorage.getItem('user'))[0]:null;
    // window.sessionStorage.removeItem('hasLogin')
    if(this.login){
      this.history = this.userMsg.seebefore;
      this.$http.post('http://127.0.0.1:9876/getAnimeById',{
        id:this.userMsg.collections
      }).then(res=>{
        this.collections = JSON.parse(res.bodyText)
        console.log(this.collections.length)
      })
      this.$http.post('http://127.0.0.1:9876/gettuijian',this.userMsg).then(res=>{
        // console.log(res)
        this.tuijian = JSON.parse(res.bodyText)
      })
    }else{
      this.history = [];
      this.collections = [];
      this.tuijian = [];
    }
  },
  data(){
    return{
      nameMsg:'',
      showErrorMsg:false,
      show3:false,
      detail:false,
      userMsg:null,
      login:false,
      showfenlei:false,
      showcollection:false,
      showhistory:false,
      showtuijian:false,
      showuser:false,
      tuijian:[],
      collections:[],
      history:[],
      hotTag:['热血','青春','冒险','恐怖','搞笑','恋爱']
    }
  },
  methods: {
    goCollections(index){
      let data = this.collections[index]
      window.sessionStorage.setItem('animeDetail',JSON.stringify(data));
      window.location.href = URL.animeDetail;
    },
    goDetail(index){
      let data = this.tuijian[index]
      window.sessionStorage.setItem('animeDetail',JSON.stringify(data));
      window.location.href = URL.animeDetail;
    },
    goSearch(index){
      let tag = this.hotTag[index];
      window.sessionStorage.setItem('findTagAnime',tag);
      window.location.href = URL.search
    },
    zhuxiao(){
      this.login = false;
      window.sessionStorage.removeItem('user');
      window.sessionStorage.removeItem('hasLogin')
      // window.location.reload
    },
    goUser(){
      if(this.login){
        window.location.href = URL.user;
      }
    },
    upDatatuijian(){
      this.$http.post('http://127.0.0.1:9876/gettuijian',this.userMsg).then(res=>{
        // console.log(res)
        this.tuijian = JSON.parse(res.bodyText)
        console.log(JSON.parse(res.bodyText))
      })
    },
    turnLog(index){
      if(index==='0'){
        window.sessionStorage.setItem('logreg','0')
      }else{
        window.sessionStorage.setItem('logreg','1')
      }
      window.location.href = URL.logreg;
    },
    enter(){
      setTimeout(()=>{
        this.show3 = true;
      },300)
    },
    leave(){
      setTimeout(() => {
      if(this.detail){
        return;
      }
      this.show3 = false;
      },300);
    },
    detailEnter(){
      if(this.show3){
        this.detail = true;
      }
    },
    detailLeave(){
      setTimeout(()=>{
        this.detail = false;
        this.show3 = false;
      },200)
    },
    onKeyUp(){
      let ev = window.event;
      if(ev.keyCode === 13){
        this.onSearch();
      }
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
      this.$http.get('http://127.0.0.1:9876/getAnime?name='+name).then(res=>{
        if(JSON.parse(res.bodyText).length !== 0){
          window.sessionStorage.setItem('mohuSearch','1')
          let bodyText = JSON.parse(res.bodyText);
          this.$emit('searchClick',bodyText);
        }
        else{
          let str = [];
          pinyin(name).forEach(data => {
            str.push(data[0].charAt(0))
          });
          this.$http.get('http://127.0.0.1:9876/getsmallname?regname='+str.join('')).then(res=>{
            window.sessionStorage.setItem('mohuSearch','0')
            let bodyText = JSON.parse(res.bodyText);
            this.$emit('searchClick',bodyText)
          })
        }
      })
    },
    turnIndex(){
      window.location.href=URL.index;
    }
  }
})
</script>
<style>
  .dili{
    position:absolute;
    top: -100%;
    background: #fff;
    padding: 5px;
    border-radius: 7px;
  }
  .dili i{
    width: 0;
    height: 0;
    border: 10px solid #000;
    position: absolute;
    top: 100%;
    left: 50%;
    transform: translateX(-50%);
    border-color: rgba(255,255,255,1) rgba(255,255,255,0) rgba(255,255,255,0) rgba(255,255,255,0);
  }
  .fade-enter-active, .fade-leave-active {
    transition: opacity .5s;
  }
  .fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
    opacity: 0;
  }
  .main-top{
    width: 100%;
    height: 150px;
    background-image: url('~@a/banner19219.jpg');
    background-repeat: no-repeat;
    border-bottom:1px solid #000;
    background-size:100% auto;
    background-position-y: 50%;
    position: fixed;
    transition: 0.2s;
    top: 0;
    left: 0;
    z-index: 999;
  }
  .inputMsg{
    width: 200px;
  }
  .searchBox{

    position: absolute;
    bottom: 10px;
    right: 30px;
  }
  .main-top p{
    position: absolute;
    bottom: 0;
    font:bold helvetica,arial,sans-serif;
    color:#F00;
    padding:10px;
    text-shadow:0 0 4px #FFF,0 -5px 4px #ff3,2px -10px 6px #fd3, -2px -15px 11px #f80, 2px -25px 18px #f20;
    cursor: pointer;
  }
  .transition-box {
    margin-bottom: 10px;
    width: 200px;
    border-radius: 4px;
    background-color: #409EFF;
    text-align: center;
    color: #fff;
    padding: 5px;
    box-sizing: border-box;
    margin-right: 20px;
  }
  .transition-box div{
    cursor: pointer;
  }
  .transition-box div:hover{
    color: peru
  }
  .userInfo{
    position: absolute;
    padding: 2px;
    width: 100%;
    height: 30px;
    left: 35%;
    bottom: 5px;
  }
  .userInfo ul{
    padding: 0;
  }
  .userInfo li{
    float: left;
    list-style: none;
    width: 80px;
    height: 20px;
    text-align: center;
    line-height: 20px;
    border-radius:10px;
    cursor: pointer;
    color: #000;
  }
  .userInfo .li_ul li{
    width: 80px;
    height: 20px;
    border-radius: 0;
    background: #fff;
    padding: 5px 0;
  }
  .userInfo .li_tuijian li{
    width: 250px;
    padding: 5px 0;
  }
  .userInfo li:hover{
    background: #409EFF
  }
  .userInfo .li_ul::after{
    clear:both;
    display: block;
    content:'';
  }
  .userInfo .li_tuijian{
    background: #fff;
    width: 250px;
    border-radius: 5px;
    padding:0;
  }
  .userInfo img:hover{
    transform: translateY(8px)
  }
  .canClick{
    cursor: pointer;
  }
  .userInfo .noCollections{
    cursor: text
  }
  .userInfo .noCollections:hover{
    background: #fff
  }

  .userInfo .li_tuijian .flash{
    color: red
  }
</style>
