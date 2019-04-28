<template>
  <!-- 播放组件 -->
  <div class="framebox">
    <h2>{{playData.name}}</h2>
    <iframe class="frameplay" :src="playData.play[playData.playNow-1]" frameborder="0"></iframe>
    <p @click="fleshPlay(playData.playNow-1,'pre')" :class="{canClick:isPreCanClick}" v-html="preStr"></p>
    <p @click="fleshPlay(playData.playNow+1,'next')" :class="{canClick:isNexCanClick}" v-html="nexStr"></p>
  </div>
</template>
<script>
export default {
  computed:{
    preStr(){
      if(this.playData.playNow===1){
        this.isPreCanClick=false;
        return '已是最先话'
      }else{
        this.isPreCanClick=true;
        return '第<span>'+(this.playData.playNow-1)+'</span>话'
      }
    },
    nexStr(){
      if(this.playData.playNow===this.playData.play.length){
        this.isNexCanClick=false;
        return '已是最终话'
      }else{
        this.isNexCanClick=true;
        return '第<span>'+(this.playData.playNow+1)+'</span>话'
      }
    }
  },
  data(){
    return{
      isPreCanClick:true,
      isNexCanClick:true,
    }
  },
  props:{
    playData:{
      type:Object,
      default:{}
    }
  },
  methods:{
    fleshPlay(nextPlayNum,flag){
      if(flag==='pre' && this.isPreCanClick || flag ==='next' && this.isNexCanClick){
        this.playData.playNow = nextPlayNum;
        this.$emit('fleshPlay',this.playData)
      }
    }
  }
}
</script>
<style>
  .framebox{
    width: 80%;
    height: 450px;
    margin: 0 auto;
    /* background: #909399; */
  }
  .framebox .frameplay{
    width:100%;
    height: 400px;
  }
  .framebox h2{
    color:#E6A23C
  }
  .framebox p{
    width: 50%;
    float: left;
    margin: 5px 0 0 0
  }
  .framebox p:nth-of-type(2){
    text-align: right;
  }
  .canClick{
    cursor:pointer;
  }
  .canClick:hover{
    transition: 0.2s;
    color: red;
  }
</style>
