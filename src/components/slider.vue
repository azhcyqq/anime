<template>
  <!-- <div v-if="false">
     <div class="block">
      <el-carousel trigger="click" height="300px" type="card" :interval="4000">
        <el-carousel-item @click="itemClick(item)" v-for="(item,index) in imgData" :key="index">
          <img class="itemImg canClick" :src="item.img" alt="">
        </el-carousel-item>
      </el-carousel>
    </div>
  </div> -->

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
      console.log(item)
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
    height: 350px;
    /* margin: 0 10px; */
  }
  .block{
    width: 960px;
    height: 350px;
    margin: 20px auto;
  }
  .canClick{
    cursor: pointer;
  }

  /* .test .el-carousel__indicator{
    width:70px;
    height:30px;
  } */
</style>
