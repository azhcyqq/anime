<template>
  <!-- 标签导航与搜索结果组件 -->
  <div>
    <div  :class="['tag-box',position]" v-if="type===0">
      <p v-if="showRecommend">按类型</p>
      <span class="canClick" @click="tagClick(item)"  v-for="(item,index) in tag" :key="index" v-show="item!=null">
        <i>{{item}}</i><el-divider direction="vertical"></el-divider>
      </span>
    </div>

    <div  :class="['tag-box',position]" v-if="type===2">
      <p v-if="showRecommend">按字母</p>
      <span class="canClick" @click="tagClick(index)"  v-for="(item,index) in 26" :key="index" v-show="item!=null">
        <i>{{String.fromCharCode('a'.charCodeAt()+index)}}</i><el-divider direction="vertical"></el-divider>
      </span>
    </div>

    <div v-if="type===1 && searchData!==null">
      <div class="anime-box">
        <div class="rankN" v-if="showRank">{{rankNum+1}}</div>
        <div>
          <img @click="jumpDetail(searchData)" class="anime-img canClick" :src="searchData.img" alt="">
        </div>
        <div class="anime-introduce">
          <p>名称：<span class="canClick" @click="jumpDetail(searchData)">{{searchData.name}}</span></p>
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
    },
    showRank:{
      type:Boolean,
      default:false,
    },
    rankNum:{
      type:Number,
      default:0
    }
  },
  methods:{
    tagClick(item){
      this.$emit('tagFind',item)
    },
    jumpDetail(dataObject){
      this.$emit('jumpDetail',dataObject)
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
    position: relative;
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
  .canClick i{
    text-decoration: none;
    font-style: normal;
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
    margin: 30px auto;
    position: relative;
    z-index: 999;
    color: #fff;
    padding: 30px 0;
    width: 550px;
  }
  .middle span{
    color: #fff;
  }
  .rankN{
    position: absolute;
    font-size: 1.8em;
    transform: rotate(25deg);
    color: brown;
    right: 10px;
    border: 1px solid #000;
    padding: 5px;
    border-radius: 50%;
    width: 50px;
    height: 37px;
    text-align: center;
    line-height: 37px;
  }
</style>
