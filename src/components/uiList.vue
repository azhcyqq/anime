<template>
<!-- 每周更新导航组件 -->
<div>
  <div class="uiList" v-if="type===0">
    <ul>
      <li v-for="(item,index) in weekList" :key="index" @click="weekClick(index)" :class="{active:weekNow===index}">{{item}}</li>
    </ul>
    <div class="content">
      <div class="nav" v-for="(item,index) in weekSelect" :key="index" v-show="weekNow===index">
        <div v-for="(it,ind) in item" :key="ind">
          <img @click="jumpImg(it)" :src="it.img" alt=""><span @click="jumpImg(it)" class="canClick itemName">{{it.name}}</span>
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
          <span @click="jumpImg(it)" class="itemName2 canClick">{{it.name}}</span>
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
      this.$http.get('http://127.0.0.1:9876/getweek').then(res=>{
        let arr = JSON.parse(res.bodyText);
        console.log(arr)
        for(let i=0;i<arr.length;i++){
          this.weekSelect[arr[i].date-1].push(arr[i])
        }
        console.log(this.weekSelect)
      }).catch(err=>{
        console.log(err)
      })
    },
    jumpImg(item){
      this.$emit('imgGo',item)
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
  .nav .itemName:hover{
    text-decoration: underline;
    color: maroon;
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
    width: 245px;
    overflow: hidden;
  }
  .uiList2 ul{
    position: relative;
  }
  .uiList2 li{
    float: left;
    width: 35px;
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
    font-size: 0.9rem;
  }
  .nav2 .itemName2:hover{
    text-decoration: underline;
    color:maroon;
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
  .canClick{
    cursor: pointer;
  }
</style>
