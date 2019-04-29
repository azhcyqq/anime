<template>
  <!-- 头部组件 -->
  <div class="main-top">
    <p @click="turnIndex">守❤护❤世❤界❤上❤最❤好❤的❤坤❤坤</p>
    <div v-if='searchShow' class="searchBox">
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
  data(){
    return{
      nameMsg:''
    }
  },
  methods: {
    onSearch(){
      let name = this.nameMsg;
      this.$http.get('http://127.0.0.1:9876/getAnime?name='+name).then(res=>{
        if(JSON.parse(res.bodyText).length !== 0){
          this.$emit('searchClick',JSON.parse(res.bodyText))
        }
        else{
          let str = [];
          pinyin(name).forEach(data => {
            str.push(data[0].charAt(0))
          });
          this.$http.get('http://127.0.0.1:9876/getsmallname?regname='+str.join('')).then(res=>{
            this.$emit('searchClick',JSON.parse(res.bodyText))
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
