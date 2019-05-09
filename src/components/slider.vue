<template>
  <!-- 首页卡片轮播图组件 -->
  <div class="test">
    <div class="block">
      <el-carousel trigger="click" height="300px" type="card" :interval="4000">
        <el-carousel-item v-for="(item,index) in imgData" :key="index">
          <img  @click="itemClick(item)" class="itemImg canClick" :src="item.img" alt="">
        </el-carousel-item>
      </el-carousel>
    </div>
  </div>
</template>
<script>
export default {
  data(){
    return{
      imgData:[]
    }
  },
  methods:{
    itemClick(item){
      this.$emit('jumpImg',item)
    }
  },
  created(){
    this.$http.get('http://127.0.0.1:9876/getlunbo').then(res=>{
      this.imgData = JSON.parse(res.bodyText)
    })
  }
}
</script>
<style>
  .itemImg{
    width: 300px;
    height: 320px;
  }
  .block{
    width: 600px;
    height: 320px;
    margin: 20px auto 50px;
  }
  .canClick{
    cursor: pointer;
  }
</style>
