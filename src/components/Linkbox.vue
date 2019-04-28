<template>
  <!-- 点击盒子组件 -->
  <div>
    <div class="box" v-if="type===0">
        <img @click="imgGo(boxData)" :src="boxData.img" alt="">
        <p class="animeName">{{boxData.name}}</p>
    </div>
  </div>
</template>
<script>
export default {
  props:{
    type:{
      type:Number,
      default:0
    },
    boxData:{
      type:Object,
      default:{}
    }
  },
  methods:{
    imgGo(boxData){
      this.$emit('goDetail',boxData)
    }
  }
}
</script>
<style>
  .box{
    width: 290px;
    height: 270px;
    position: relative;
  }
  .box img{
    position: absolute;
    width: 290px;
    height: 270px;
    cursor: pointer;
  }
  .box img:hover+p{
    transition: 0.2s;
    background:#0B2744;
    transform: scale(1.01)
  }
  .box img:hover{
    transition: 0.2s;
    transform: scale(1.01)
  }
  .animeName{
    font-size: 18px;
    font-weight: bold;
    text-align: center;
    position: relative;
    top: 100%;
    transform: translateY(-100%);
    color: antiquewhite;
    background: rgba(0,0,0,0.5)
  }
</style>
