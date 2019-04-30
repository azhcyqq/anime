<template>
  <!-- 头部组件 -->
  <div class="main-top">
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
      default:true
    }
  },
  created() {
    window.addEventListener('keyup',this.onKeyUp)
  },
  data(){
    return{
      nameMsg:'',
      showErrorMsg:false,
    }
  },
  methods: {
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
    height: 100px;
    background-image: url('~@a/banner19219.jpg');
    background-repeat: no-repeat;
    border-bottom:1px solid #000;
    background-size:100% auto;
    background-position-y: 50%;
    position: relative;
  }
  .inputMsg{
    width: 200px;
  }
  .searchBox{

    position: absolute;
    bottom: 20px;
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
</style>
