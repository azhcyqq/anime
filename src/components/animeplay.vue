<template>
  <!-- 播放集数组件 -->
  <div class="anime-play-list">
    <p v-for="(item,index) in dataObject.titles" :key="index" @click="play(index)"><span>{{item}}</span></p>
  </div>
</template>
<script>
export default {
  data(){
    return{

    }
  },
  methods:{
    play(index){
      this.$emit('goPlay',this.dataObject,index)
    }
  },
  props:{
    dataObject:{
      type:Object,
      default:{}
    }
  },
  created(){
  }
}
</script>
<style>
  .anime-play-list{
    display:flex;
    justify-content: space-between;
    flex-wrap: wrap;
    width: 760px;
    margin: 0 auto 20px;
  }
  .anime-play-list p{
    width:48%;
    position: relative;
    margin-top: 3%;
    cursor: pointer;
  }
  .anime-play-list span:hover {
    transition: 0.3s;
    color: rgba(148, 41, 35, 0.8)
  }
</style>

