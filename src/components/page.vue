<template>
  <!-- 页面分页导航栏（最下方） -->
  <div v-if="showPage" class="pageDiv">
    <el-pagination
      :current-page="currentPage"
      @current-change="currentChange"
      background
      layout="prev, pager, next"
      :total="maxLen*10">
    </el-pagination>
  </div>
</template>
<script>
export default {
  data() {
    return {

    }
  },
  props:{
    maxLen:{
      type:Number,
      default:0
    },
    showPage:{
      type:Boolean,
      default:false
    },
    currentPage:{
      type:Number,
      default:1
    }
  },
  methods: {
    currentChange(page){
      this.$emit('currentChange',page)
    }
  },
}
</script>
<style>
  .pageDiv{
    position:relative;
    margin:0 auto;
  }
</style>
