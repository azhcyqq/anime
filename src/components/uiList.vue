<template>
<div>
  <div class="uiList" v-if="type===0">
    <ul>
      <li v-for="(item,index) in weekList" :key="index" @click="weekClick(index)" :class="{active:weekNow===index}">{{item}}</li>
    </ul>
    <div class="content">
      <div class="nav" v-for="(item,index) in weekSelect" :key="index" v-show="weekNow===index">
        <div v-for="(it,ind) in item" :key="ind">
          <img :src="it.img" alt=""><span class="itemName">{{it.name}}</span>
          <span class="allNum">共{{it.play.length}}集</span>
        </div>
      </div>
    </div>
  </div>
  <div class="uiList2" v-if="type===1">
    <ul>
      <li v-for="(item,index) in weekList" :key="index" @click="weekClick(index)" :class="{active:weekNow===index}">{{item}}</li>
    </ul>
    <div class="content2">
      <div class="nav2" v-for="(item,index) in weekSelect" :key="index" v-show="weekNow===index">
        <div v-for="(it,ind) in item" :key="ind">
          <span class="itemName2">{{it.name}}</span>
          <span class="allNum2">共{{it.play.length}}集</span>
        </div>
      </div>
    </div>
  </div>
</div>
</template>
<script>
export default {
  props:{
    //0大图标模式 1小图标模式
    type:{
      type:Number,
      default:'0'
    }
  },
  data(){
    return{
      weekList:['一','二','三','四','五','六','日'],
      weekSelect:[],
      weekNow:0,
    }
  },
  methods:{
    weekClick(index){
      this.weekNow = index
    },
    init(){
      for(let i=0;i<this.weekList.length;i++){
        this.weekSelect.push([]);
      }
      this.$http.get('http://127.0.0.1:9876/getAnime').then(res=>{
        console.log(JSON.parse(res.bodyText))
        let arr = JSON.parse(res.bodyText);
        for(let i=0;i<arr.length;i++){
          this.weekSelect[i%this.weekList.length].push(arr[i])
        }
      }).catch(res=>{
        console.log(res)
      })
      // console.log(this)
    }
  },
  created(){
    this.init();
  }
}
</script>
<style>
  .uiList{
    width: 560px;
    overflow: hidden;
  }
  .uiList ul{
    position: relative;
  }
  .uiList li{
    float: left;
    width: 80px;
    height:30px;
    text-decoration: none;
    list-style: none;
    text-align: center;
    cursor: pointer;
    line-height: 30px;
  }
  .active{
    background:lightblue;
  }
  .uiList ul::after{
    display: block;
    content: '';
    clear: both;
  }
  .nav div{
    line-height: 100%;
    position: relative;
  }
  .nav img{
    width: 70px;
    height: 70px;
    border-radius: 50%;
  }
  .nav .itemName{
    display: inline-block;
    height:100%;
    position: absolute;
    top: 50%;
    transform: translateY(-50%);
    line-height: 70px;
    margin-left: 20px
  }
  .nav .allNum{
    display: inline-block;
    height:100%;
    position: absolute;
    top: 50%;
    transform: translateY(-50%);
    line-height: 70px;
    right: 20px;
    font-size: 0.8em;
  }
  .nav div{
    margin-top: 10px;
  }


  .uiList2{
    width: 210px;
    overflow: hidden;
  }
  .uiList2 ul{
    position: relative;
  }
  .uiList2 li{
    float: left;
    width: 30px;
    text-decoration: none;
    list-style: none;
    text-align: center;
    cursor: pointer;
  }
  .active{
    background:lightblue;
  }
  .uiList2 ul::after{
    display: block;
    content: '';
    clear: both;
  }
  .nav2 div{
    line-height: 100%;
    position: relative;
  }
  .nav2 .itemName2{
    display: inline-block;
    height:100%;
  }
  .nav2 .allNum2{
    display: inline-block;
    height:100%;
    font-size: 0.8em;
    position: absolute;
    right: 0;
  }
  .nav2 div{
    margin-top: 10px;
  }
</style>
