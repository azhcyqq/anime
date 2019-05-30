<template>
  <div>
    <header-vue :searchShow="false"></header-vue>
    <div class="anime">
      <div class="anime-detail">
        <div class="anime-detail-img">
          <img :src="searchData.img" >
        </div>
        <div class="anime-detail-intro">
          <div>
            <h2>{{searchData.name}}</h2>
            <span>地区：日本</span>
            <span>年份：{{year}}</span>
            <span>标签：<a class="canClick" @click="jumpTo(item)" :key="index" v-for="(item,index) in searchData.tag">{{item}}</a></span>
            <span></span>
            <span class="introduce">简介：{{searchData.introduce}}</span>
          </div>
        </div>
      </div>
      <div class="anime-title">
        <span>在线</span>
        <span class="collection" @click="collect">{{collection}}</span>
        <el-divider></el-divider>
      </div>
      <div class="anime-play">
        <animeplay-vue @goPlay="playGo" :dataObject="searchData"></animeplay-vue>
      </div>
      <el-divider></el-divider>
      <h2 style="text-align:center">评论区</h2>
      <div class="comment">
        <textarea v-model="comment_content" placeholder="请尽管吐槽" cols="30" rows="10"></textarea>
        <input @click="addComment" type="button" value="发布评论">
        <el-divider></el-divider>
        <div v-if="comment.length>=1" class="comment_content">
          <div v-for="(item,index) in comment" :key="index">
            <p class="commenter">{{item.commenter}}</p>
            <p class="content">{{item.content}}</p>
            <p class="time">{{item.time}}</p>
            <el-divider></el-divider>
          </div>
        </div>
        <div v-if="comment.length === 0">
          <p style="text-align:center;color:rgb(153, 162, 170);font-size:18px;">无人，快抢沙发</p>
        </div>
      </div>
      <div class="linkBox-box">
        <el-carousel height="250px" :autoplay="false">
          <el-carousel-item v-for="(item,index) in 3" :key="index" >
            <div>
              <img class="boxImg canClick" @click="imgGo(index,ind,hotSuggest)" v-for="(it,ind) in 5"
              :key="ind" :src="hotSuggest[5*index+ind].img" :alt="hotSuggest[5*index+ind].name">
            </div>
          </el-carousel-item>
        </el-carousel>
      </div>
    </div>
    <footer-vue></footer-vue>
  </div>
</template>

<script>
import headerVue from '@com/header'
import footerVue from '@com/footer'
import searchBoxVue from '@com/searchBox'
import pageVue from '@com/page'
import animeplayVue from '@com/animeplay'
import linkboxVue from '@com/Linkbox'
import URL from '@mock/url.json'
export default {
    data(){
      return{
        searchData:null,
        year:0,
        hotSuggest:null,
        collection:'',
        userMsg:null,
        login:false,
        comment:[],
        comment_content:''
      }
    },
    components: {
      headerVue,
      footerVue,
      searchBoxVue,
      pageVue,
      animeplayVue,
      linkboxVue,
    },
    created() {
      //获取上一页传递的数据；
      this.searchData = JSON.parse(window.sessionStorage.getItem('animeDetail'));
      this.comment = this.searchData.comment;
      console.log(this.comment)
      this.login = window.sessionStorage.getItem('hasLogin')?true:false;
      this.userMsg = window.sessionStorage.getItem('user')?JSON.parse(window.sessionStorage.getItem('user'))[0]:null;
      if(this.login && this.userMsg.collections.includes(this.searchData.unitID)){
        this.collection = "已收藏"
      }else{
        this.collection = "收藏"
      }
      //获取年份
      this.year = Math.round(Math.random()*10+2000);
      //获取热门推荐
      this.$http.get('http://127.0.0.1:9876/gethot?small=1&page='+Math.round( (Math.random()*10)+10 ) ).then(res=>{
        this.hotSuggest = JSON.parse(res.bodyText)
      })
    },
    methods: {
      addComment(){
        if(this.login && this.comment_content!=''){
          let date = new Date();
          let year = date.getFullYear();
          let day = date.getDate();
          let month = date.getMonth()+1;
          let hours = date.getHours()<10?'0'+date.getHours():date.getHours();
          let min = date.getMinutes()<10?'0'+date.getMinutes():date.getMinutes();
          let second = date.getSeconds()<10?'0'+date.getSeconds():date.getSeconds();
          let comment_time = ''+year+'-'+month+'-'+day+' '+hours+':'+min+':'+second
          let json = {
            commenter:this.userMsg.username,
            content:this.comment_content,
            time:comment_time
          }
          this.searchData.comment.unshift(JSON.parse(JSON.stringify(json)))
          this.$http.post('http://127.0.0.1:9876/addComment',this.searchData).then(res=>{
            this.$message.success('发布评论成功')
            this.comment = this.searchData.comment;
            window.sessionStorage.setItem('animeDetail',JSON.stringify(this.searchData))
          })
        }else if(!this.login){
          this.$message.error('请登录后进行评论')
        }
        else if(this.comment_content === ''){
          this.$message.error('请输入内容')
        }
      },
      collect(){
        if(this.userMsg.collections.includes(this.searchData.unitID)){
          this.userMsg.collections.splice(this.userMsg.collections.indexOf(this.searchData.unitID),1)
          this.collection = "收藏"
        }else{
          this.userMsg.collections.push(this.searchData.unitID)
          this.collection = "已收藏"
        }
        console.log(this.userMsg)
        this.$http.post('http://127.0.0.1:9876/update',this.userMsg).then(res=>{
          window.sessionStorage.setItem('user','['+JSON.stringify(this.userMsg)+']')
        })
      },
      //点击标签跳转至搜索页面
      jumpTo(tag){
        window.sessionStorage.setItem('findTagAnime',tag)
        window.location.href = URL.search;
      },
      //点击推荐跳转至对应详情页面
      imgGo(index,ind,hotSuggest){
        window.sessionStorage.setItem('animeDetail',JSON.stringify(hotSuggest[index*5+ind]))
        window.location.reload();
      },
      //点击集数跳转至播放页面
      playGo(animeData,index){
        animeData.playNow = index+1;
        window.sessionStorage.setItem('playData',JSON.stringify(animeData))
        window.location.href = URL.animeplay
      }
    }
}
</script>
<style>
  *{
    margin: 0;
    padding: 0;
  }
  .anime{
    margin:20px;
    margin-bottom: 50px;
    margin-top: 170px;
  }
  .anime-detail{
    display: inline-block;
    position: relative;
    left: 50%;
    transform: translate(-50%)
  }
  .anime .anime-detail .anime-detail-img img{
    width:220px;
  }
  .anime .anime-detail .anime-detail-img::after,.anime-detail::after{
    display:block;
    content:'';
    clear:both;
  }
  .anime .anime-detail .anime-detail-intro,.anime .anime-detail .anime-detail-img{
    margin-left:30px;
    /* float:left; */
    display:inline-block;
  }

  .anime .anime-detail .anime-detail-intro{
    width:480px;
  }
  .anime .anime-detail .anime-detail-intro h2{
    margin:0;
    color:#E6A23C
  }
  .anime .anime-detail .anime-detail-intro span{
    display:inline-block;
    width:50%;
    height:30px;
    line-height:30px;
    /* float:left; */
    margin-top:5px;
  }
  .anime .anime-detail .anime-detail-intro span a{
    margin-left:5px;
  }
  .anime .anime-detail .anime-detail-intro .introduce{
    width:100%
  }
  .canClick{
    cursor:pointer;
    color: rgba(213, 21, 21,0.8);
  }
  .canClick:hover{
    transition: 0.2s;
    color: red;
  }
  .linkBox-box{
    width: 1050px;
    height: 100%;
    position: relative;
    z-index: 99;
    margin: 0 auto;
  }
  .linkBox-box::after{
    display: block;
    content: '';
    clear: both;
  }
  .boxImg{
    width: 180px;
    height: 250px;
    margin: 0 10px;
  }
  .el-carousel{
    width: 1000px;
    margin: 0 auto;
  }
  .anime-play{
    margin: 0 auto;
  }
  .canClick{
    cursor: pointer;
  }
  .anime-title{
    width: 760px;
    margin: 20px auto 0
  }
  .el-divider--horizontal{
    margin:  18px 0 0 0;
  }
  .anime-title span{
    display: inline-block;
    width: 50px;
    height: 30px;
    line-height: 30px;
    text-align: center;
    background: #f68;
    border-radius: 10px 10px 0 0;
    margin-left: 50px;
  }
  .anime-title .collection{
    background: #fff;
    cursor: pointer;
  }
  .comment{
    width: 760px;
    margin: 10px auto;
    border: 1px solid #000;
    position: relative;
    padding-top: 30px;
  }
  .comment::after{
    display: block;
    content: '';
    clear: both;
  }
  .comment textarea{
    width: 100%;
    height: 70px;
    resize: none;
  }
  .comment input{
    position: absolute;
    right: 10px;
    top: 5px;
  }
  .comment_content{
    margin-bottom: 20px;
  }
  .comment_content .commenter{
    color: #6d757a;
    font-size: 12px;
  }
  .comment_content .commenter:hover{
    color: #00a1d6;
  }
  .comment_content .content{
    text-indent: 20px;
    font-size: 14px;
    color: #000;
  }
  .comment_content .time{
    color:rgb(153, 162, 170);
    font-size: 12px;
  }
</style>
