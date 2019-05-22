<template>
  <div>
    <div class="login" v-if="isregiest">
      <div class="inputClass">
        <div><p>用户名：</p><el-input
          placeholder="输入用户名"
          v-model="username"
          clearable minlength="6" maxlength="15" @blur="unitSure">
        </el-input></div>
        <div><p>密码：</p><el-input
          placeholder="输入密码"
          v-model="pwd1"
          show-password minlength="5" maxlength="15">
        </el-input></div>
        <div><p>再次确认密码：</p><el-input
          placeholder="再次确认密码"
          v-model="pwd2"
          show-password minlength="5" maxlength="15">
        </el-input></div>
        <div><p>手机号码：</p><el-input
          placeholder="输入手机号码"
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
    <div v-if="!isregiest" class="login">
      <div class="inputClass">
        <div><p>用户名：</p><el-input
          placeholder="请输入用户名"
          v-model="username"
          clearable minlength="6" maxlength="15">
        </el-input></div>
        <div><p>密码：</p><el-input
          placeholder="请输入密码"
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
  import URL from '@mock/url.json'
  import crypto from 'crypto'
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
        this.pwd3 = '';
        this.phoneNum = '';
      },
      login(){
        this.$http.post('http://127.0.0.1:9876/login',{
          username:this.username,
          password:this.aesEncrypt(this.pwd3)
        }).then(res=>{
          if(res.bodyText==='[]'){
            alert('用户名或密码错误')
          }else{
            window.sessionStorage.setItem('user',res.bodyText)
            window.sessionStorage.setItem('hasLogin','1')
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
          password:this.aesEncrypt(this.pwd1),
          phone:this.phone,
          favority:this.checkboxGroup1,
        }).then(res=>{
          let msg = JSON.parse(res.bodyText)
          if(msg.ok){
            this.$message({
              message: '注册成功',
              type: 'success'
            });
          }
          this.isregiest = false;
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
      },
      //加密
      aesEncrypt:function(encrypted, key) {
        var md5 = crypto.createHash("md5");
        md5.update(encrypted);
        var a = md5.digest('hex');
        return a ;
      },
      //解密
      aesDecrypt:function(encrypted, key) {
        const decipher = crypto.createDecipher('aes192', key);
        var decrypted = decipher.update(encrypted, 'hex', 'utf8');
        decrypted += decipher.final('utf8');
        return decrypted;
      }
    },
    created(){
      this.init();
      this.isregiest = window.sessionStorage.getItem('logreg') === '0'?false:true;
      console.log(this.isregiest)
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

