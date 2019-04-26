<template>
  <div class="foot">
    <p>友情链接</p>
    <div>
      <a href="http://www.dilidili.name/">D嘀哩嘀哩</a>
      <a href="https://www.bilibili.com/">哔哩哔哩动画</a>
      <a href="http://www.dilidili.name/">D嘀哩嘀哩</a>
      <a href="https://www.bilibili.com/">哔哩哔哩动画</a>
      <a href="http://www.dilidili.name/">D嘀哩嘀哩</a>
      <a href="https://www.bilibili.com/">哔哩哔哩动画</a>
    </div>
  </div>
</template>
<script>
export default {

}
</script>
<style>
  .foot{
    width: 100%;
    height: 100px;
    background: #ccc;
    position: fixed;
    bottom: 0;
    z-index: 999;
  }
  .foot p{
    padding-top: 20px;
    margin-left: 30px;
    margin-top: 0;
    text-align: center;
    color:maroon;
  }
  .foot div{
    margin-top: 10px;
    text-align: center;
  }
  .foot a{
    display: inline-block;
    text-decoration: none;
    list-style: none;
    color: rgb(46, 45, 45);
    height: 30px;
    margin: 0 10px;
    line-height: 30px;
  }
  .foot a:hover{
    color:maroon;
    transition: 0.2s
  }
</style>
