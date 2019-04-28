<template>
  <!-- 广告图组件 -->
  <div v-if="isAppear">
    <div :class="imgPosition">
      <img class="banner-img" :src="imgSrc" alt="">
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
    }
  }
}
</script>
<style>
  .banner-left{
    position: absolute;
    width: 10%;
    height: 300px;
    left: 10px;
    top: 50%;
    transform: translateY(-50%);
  }
  .banner-right{
    position: absolute;
    width: 200px;
    height: 300px;
    right: 10px;
    top: 50%;
    transform: translateY(-50%);
  }
  .banner-middle{
    position: absolute;
    width: 200px;
    height: 300px;
    left: 50%;
    top:50%;
    transform: translate(-50%,-50%)
  }
  .banner-img{
    width: 200px;
    height: 300px;
  }
  .el-icon-error::before{
    position: absolute;
    right: 0;
    top: 0;
    color: red;
  }
</style>
