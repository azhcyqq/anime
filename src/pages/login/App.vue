<template>
  <div>
    <div class="login" v-if="isregiest">
      <div class="inputClass">
        <div><p>用户名：</p><el-input
          placeholder="请输入内容"
          v-model="username"
          clearable minlength="6" maxlength="15" @blur="unitSure">
        </el-input></div>
        <div><p>密码：</p><el-input
          placeholder="请输入内容"
          v-model="pwd1"
          show-password minlength="5" maxlength="15">
        </el-input></div>
        <div><p>再次确认密码：</p><el-input
          placeholder="请输入内容"
          v-model="pwd2"
          show-password minlength="5" maxlength="15">
        </el-input></div>
        <div><p>手机号码：</p><el-input
          placeholder="请输入内容"
          v-model="phoneNum"
          clearable minlength="11" maxlength="11">
        </el-input></div>
      </div>
      <div>
        <p>喜欢内容：</p>
        <el-checkbox-group v-model="checkboxGroup1">
          <el-checkbox-button v-show="tags !== null" v-for="(tags,index) in tag" :label="tags" :key="index">{{tags}}</el-checkbox-button>
        </el-checkbox-group>
      </div>
      <div class="btnClass">
        <el-button type="primary" @click="regiest" :disabled="!isRight">注册</el-button>
        <el-button @click="changeRegiest">登录</el-button>
      </div>
    </div>
    <div v-if="!isregiest">
      <div class="inputClass">
        <div><p>用户名：</p><el-input
          placeholder="请输入内容"
          v-model="username"
          clearable minlength="6" maxlength="15">
        </el-input></div>
        <div><p>密码：</p><el-input
          placeholder="请输入内容"
          v-model="pwd3"
          show-password minlength="5" maxlength="15">
        </el-input></div>
      </div>
      <div class="btnClass">
        <el-button @click="changeRegiest">注册</el-button>
        <el-button @click="login" :disabled="canUse">登录</el-button>
      </div>
    </div>
  </div>
</template>
<script>
  import md5 from '@/md5/md5.js'
  import URL from '@mock/url.json'
  export default {
    data(){
      return{
        username:'',
        pwd1:'',
        pwd2:'',
        pwd3:'',
        phoneNum:'',
        tag:[],
        checkboxGroup1:[],
        userMsg:{},
        loading:true,
        isNameCanUse:true,
        isregiest:true,
      }
    },
    methods:{
      changeRegiest(){
        this.isregiest = !this.isregiest;
        this.username = '';
        this.pwd1 = '';
        this.pwd2 = '';
        this.phoneNum = '';
      },
      login(){
        this.$http.post('http://127.0.0.1:9876/login',{
          username:this.username,
          pwd1:md5.aesEncrypt(pwd1,'pwd')
        }).then(res=>{
          if(res.bodyText==='[]'){
            alert('用户名或密码错误')
          }else{
            window.sessionStorage.setItem('user',JSON.parse(res.bodyText))
            window.location.href = URL.index;
          }
        })
      },
      init(){
        this.$http.get('http://127.0.0.1:9876/getTag').then(res=>{
          this.tag = JSON.parse(res.bodyText)
        });
      },
      regiest(){
        this.$http.post('http://127.0.0.1:9876/regiest',{
          username:this.username,
          password:md5.aesEncrypt(pwd1,'pwd'),
          phone:this.phone,
          favority:this.checkboxGroup1,
        }).then(res=>{
          console.log(res)
        })
      },
      unitSure(){
        if(this.loading){
          this.loading = false;
          this.$http.get('http://127.0.0.1:9876/unitsure?username='+this.username).then(res=>{
            if(res.bodyText === '[]'){
              this.$message.success('该用户名可用')
              this.isNameCanUse = true;
            }else{
              this.$message.error('用户名已被注册')
              this.isNameCanUse = false;
            }
            this.loading = true;
          })
        }
      }
    },
    created(){
      this.init();
      console.log(this.isRight)
    },
    computed:{
      phoneRight(){
        var phone = this.phoneNum
        if(!(/^1[34578]\d{9}$/.test(phone))){
            return false;
        }
        return true;
      },
      isRight(){
        if(this.isNameCanUse && this.pwd1 === this.pwd2 && this.phoneRight){
          return true;
        }
        return false;
      },
      canUse(){
        if(this.username.length>0 && this.pwd3.length>0){
          return false;
        }
        return true;
      }
    }
  }
</script>
<style>
  .login{
    width: 500px;
    margin: 0 auto;
  }
  .login p{
    text-align: center;
  }
  .btnClass{
    display: flex;
    justify-content: space-around;
    margin-top: 20px;
  }
  .inputClass{
    width: 300px;
    margin: 0 auto;
  }
  .el-checkbox-button__inner{
    margin-left: 2px;
  }
</style>

