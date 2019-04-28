<template>
  <!-- 标签导航与搜索结果组件 -->
  <div>
    <div  :class="['tag-box',position]" v-if="type===0">
      <p v-if="showRecommend">推荐</p>
      <span class="canClick" @click="tagClick(item)"  v-for="(item,index) in tag" :key="index" v-show="item!=null">
        {{item}}
      </span>
    </div>

    <div v-if="type===1 && searchData!==null">
      <div class="anime-box">
        <div>
          <img @click="jumpDetail" class="anime-img canClick" :src="searchData.img" alt="">
        </div>
        <div class="anime-introduce">
          <p>名称：<span class="canUse" @click="jumpDetail">{{searchData.name}}</span></p>
          <p>标签：<span class="canClick smallTag" @click="tagClick(item)" v-for="(item,index) in searchData.tag" :key="index">{{item}}</span></p>
          <p>简介：<span>{{introduce}}</span></p>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  props:{
    // type：0标签导航
    // type：1搜索结果
    type:{
      type:Number,
      default:0
    },
    searchData:{
      type:Object,
      default:null
    },
    showRecommend:{
      type:Boolean,
      default:true
    },
    position:{
      type:String,
      default:''
    }
  },
  methods:{
    tagClick(item){
      this.$emit('tagFind',item)
    },
    jumpDetail(){
      window.sessionStorage.setItem('animeDetail',JSON.stringify(this.searchData));
      window.location.href = "http://127.0.0.1:8080/animeDetail.html";
    }
  },
  data(){
    return{
      tag:[],
    }
  },
  created(){
    if(this.type === 0){
      this.$http.get('http://127.0.0.1:9876/getTag').then(res=>{
        this.tag = JSON.parse(res.bodyText)
      });
    }
  },
  computed: {
    introduce(){
      if(this.type === 1 && this.searchData!==null){
        if(this.searchData.introduce.length>75){
          return this.searchData.introduce.slice(0,75)+'······';
        }else{
          return this.searchData.introduce;
        }
      }
      return ''
    }
  }
}
</script>
<style>
  .tag-box{
    margin: 20px 0;
    width: 350px;
  }
  .tag-box p{
    padding: 5px;
  }
  .tag-box span{
    padding: 5px;
    display: inline-block;
    color: #666
  }
  .anime-box{
    margin-top: 20px;
  }
  .anime-box .anime-img,.anime-box .anime-introduce{
    float: left;
  }
  .anime-box .anime-img{
    width: 120px;
    height: 160px;
  }
  .anime-box div{
    float: left;
  }
  .anime-box::after{
    clear: both;
    display: block;
    content: '';
  }
  .anime-introduce{
    width: 650px;
    height: 150px;
    margin-left: 20px;
  }
  .anime-introduce p{
    margin: 0;
    margin-bottom: 30px;
  }
  .canClick{
    cursor: pointer;
  }
  .canClick:hover{
    transition: 0.2s;
    color: #c40e26;
  }
  .smallTag{
    padding: 0 6px;
    color: #666;
  }
  .middle{
    margin: 20px auto;
    position: relative;
    z-index: 999;
    color: #fff;
    padding: 30px 0;
    width: 550px;
  }
  .middle span{
    color: #fff;
  }
</style>
