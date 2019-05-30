<template>
  <div>
    <header-vue :showSearch="false"></header-vue>
      <div class="mt170">
        <el-container>
        <el-header>
          <div class="infoChange">

          </div>
        </el-header>
        <el-container>
          <el-aside width="200px">
            <div class="guide">
              <el-menu
                default-active="2"
                class="el-menu-vertical-demo">
                <el-menu-item @click="showIndex = '1'" index="1-1">我的信息</el-menu-item>
                <el-menu-item @click="showIndex = '2'" index="1-2">收藏</el-menu-item>
                <!-- <el-menu-item @click="showIndex = '3'" index="1-3">观看记录</el-menu-item> -->
              </el-menu>
            </div>
          </el-aside>
          <el-main>
            <div class="content">
              <div v-if="showIndex === '1'">
                <div class="content_user_main">
                  <p>用户名：</p><input type="text" v-model="username">
                  <p>手机号码：</p><input type="text" v-model="phone">
                  <p> 标签爱好</p>
                  <el-checkbox-group class="favority" v-model="checkboxGroup1">
                    <el-checkbox-button :class="{'is-checked':favority.includes(tags)}" v-show="tags !== null" v-for="(tags,index) in tag" :label="tags" :key="index">{{tags}}</el-checkbox-button>
                  </el-checkbox-group>
                  <div v-if="isshow" class="content_user_bottom">
                    <span class="modify" @click="updateUserMsg">提交修改</span>
                  </div>
                </div>
              </div>
              <div v-if="showIndex === '2'" class="collections">
                <div v-for="(item,index) in collections" :key="index">
                  <img :src="item.img" alt="" @click="goDetail(item)">
                </div>
              </div>
              <!-- <div v-if="showIndex === '3'">

              </div> -->
            </div>
          </el-main>
        </el-container>
      </el-container>
     </div>
  </div>
</template>
<script>
import URL from '@mock/url.json'
import headerVue from '@com/header'
export default {
  data(){
    return{
      showIndex:'1',
      tag:[],
      checkboxGroup1:[],
      userMsg:null,
      isNameCanUse:true,
      isregiest:true,
      collection:[],
      seebefore:[],
      favority:[],
      username:'',
      phone:'',
    }
  },
  components:{
    headerVue
  },
  methods:{
    init(){
      this.login = window.sessionStorage.getItem('hasLogin')?true:false;
      this.userMsg = window.sessionStorage.getItem('user')?JSON.parse(window.sessionStorage.getItem('user'))[0]:null;
      console.log(this.userMsg)
      this.favority = this.userMsg.favority;
      this.checkboxGroup1 = this.favority;
      this.username = this.userMsg.username;
      this.phone = this.userMsg.phone;
      this.$http.get('http://127.0.0.1:9876/getTag').then(res=>{
        this.tag = JSON.parse(res.bodyText)
      });
      this.$http.post('http://127.0.0.1:9876/getAnimeById',{
        id:this.userMsg.collections
      }).then(res=>{
        this.collections = JSON.parse(res.bodyText)
        console.log(this.collections)
      })
      this.$http.post('http://127.0.0.1:9876/getAnimeById',{
        id:this.userMsg.seebefore
      }).then(res=>{
        this.seebefore = JSON.parse(res.bodyText)
      })
    },
    goDetail(data){
      window.sessionStorage.setItem('animeDetail',JSON.stringify(data));
      window.location.href = URL.animeDetail;
    },
    updateUserMsg(){
      // this.userMsg.username = this.username;
      this.userMsg.phone = this.phone;
      this.userMsg.favority = this.checkboxGroup1;
      console.log(this.userMsg)
      this.$http.post('http://127.0.0.1:9876/update?name='+this.username,this.userMsg).then(res=>{
        this.$message.success('修改成功')
        this.userMsg.username = this.username
        window.sessionStorage.setItem('user','['+JSON.stringify(this.userMsg)+']')
        this.isshow = false;
      })

    }
  },
  created(){
    this.init();
  },
  computed:{
    isshow(){
      if(this.userMsg.username === this.username && this.userMsg.phone === this.phone && this.userMsg.favority === this.checkboxGroup1){
        return false;
      }else{
        return true;
      }
    }
  }
}
</script>
<style>
  .mt170{
    margin: 170px;
  }
  .content_user_main{
    position: relative;
  }
  .content_user_main input:disabled{
    color: #000;
    background: #fff
  }
  .content_user_bottom{
    position: absolute;
    right: 0;
    bottom: 0;
  }
  .modify{
    cursor: pointer;
    background: #cecece;
    padding: 5px;
    border-radius: 5px;
  }
  .collections img{
    width: 100px;
    height: 100px;
    cursor: pointer;
  }
  .favority{
    width: 500px;
  }
</style>
