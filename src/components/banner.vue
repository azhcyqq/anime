<template>
  <!-- 广告图组件 -->
  <div v-if="isAppear">
    <div :class="imgPosition">
      <img class="banner-img" @click="goBanner" :src="imgSrc" alt="">
      <i @click="hideBanner" class="el-icon-error"></i>
    </div>
  </div>
</template>
<script>
export default {
  props:{
    imgPosition:{
      type:String,
      default:'banner-middle'
    },
    imgSrc:{
      type:String,
      default:''
    }
  },
  data(){
    return{
      isAppear:true,
    }
  },
  methods:{
    hideBanner(){
      this.isAppear = false;
    },
    goBanner(){
      if(this.imgSrc.indexOf('tianmao')>-1){
        window.open('https://www.tmall.com')
      }else{
        window.open('https://www.jd.com')
      }
      this.isAppear = false;
    }
  }
}
</script>
<style>
  .banner-left{
    position: fixed;
    width: 200px;
    height: 300px;
    left: 10px;
    top: 50%;
    transform: translateY(-50%);
    z-index: 999;
  }
  .banner-right{
    position: fixed;
    width: 200px;
    height: 300px;
    right: 10px;
    top: 50%;
    transform: translateY(-50%);
    z-index: 999;
  }
  .banner-middle{
    position: fixed;
    width: 200px;
    height: 300px;
    left: 50%;
    top:50%;
    transform: translate(-50%,-50%);
    z-index: 999;
  }
  .banner-img{
    width: 200px;
    height: 300px;
    cursor: pointer;
  }
  .el-icon-error::before{
    position: absolute;
    right: 0;
    top: 0;
    color: red;
    cursor: pointer;
  }
</style>
