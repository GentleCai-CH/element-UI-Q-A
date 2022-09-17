
<template>
  <el-row class="bg">
    <el-col :xs="24" :sm="12" :xl="12" class="login-box">
      <el-form :model="user" ref="user" status-icon label-width="150px">
        <el-form-item><span>登录</span></el-form-item>
        <el-form-item label="用户名" prop="account"
                      :rules="rules.account">
          <el-input  v-model='user.account' placeholder='请输入用户名，默认：gentle' autocomplete='off'></el-input >
        </el-form-item>
        <el-form-item label="密码" prop="password" :rules="rules.password">
          <el-input type="password" v-model="user.password" placeholder="请输入密码，默认：2406" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item>
          
        </el-form-item>
        <!--
          
          <el-form-item>里面嵌入 <el-link>会导致 input输入框不能输入、删除

          <el-ink rel="nofollow" style="float:right" :underline="false" @click="forget">忘记密码</el-ink>
          <el-ink rel="nofollow" style="float:right" :underline="false" @click="toRegister">注册</el-ink>
        -->
        <el-form-item>
          <el-button type="primary" @click="toLogin" :loading="loginLoading" round>立即登录</el-button>
        </el-form-item>
      </el-form>
      <div  class="other-way">
        <h3>其他方式注册</h3>
        <div>
          <li><a href="javascript:void(0)"><i class="icon-qq"></i>QQ登录</a></li>
          <li><a href="javascript:void(0)"><i class="icon-blog"></i>微博登录</a></li>
          <li><a href="javascript:void(0)"><i class="icon-wechat"></i>微信登录</a></li>
        </div>
      </div>
    </el-col>

    <el-dialog
     title="找回密码"
     :visible.sync="forgetPassword"
     width="80%"
     :before-close="hideForgetPasswordDialog"
     center>
     <el-form :model="forgetForm" ref="forgetForm" status-icon label-width="100px">
      <el-form-item label="邮箱" prop="email" 
                    :rules=rules.email>
      <el-input v-model="forgetForm.email" auto-complete="off"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="success" :loading="loading" @click="sendEmailCode" plain>发送验证码</el-button>
        <el-button :loading="loading" @click.native="hideForgetPasswordDialog" plain>取消</el-button>
      </el-form-item>
     </el-form>
    </el-dialog>
  </el-row>
  <!-- 没有使用 elementUI之前自写的 登录表单
    
  <div>
    <div class="login-box">
      <h3 class="title">登录</h3>
      <div class="clear"></div>
      <div class="login-form">
        <form>
          <div class="user-name">
            <label for="user"><i class="icon-user"></i><span style="color:red;margin-left:5px">*</span></label>
            <input id="user" v-model="user" type="text" placeholder="请输入用户名，默认：gentle">
          </div>
          <div class="user-pass">
            <label for="password"><i class="icon-pass"></i><span style="color:red;margin-left:5px">*</span></label>
            <input id="password" v-model="password" type="password" placeholder="请输入密码：默认：2406">
          </div>
        </form>
      </div>
      <div class="note-pass">
        <label for="note-pass"><input id="note-pass" type="=checkbox">记住密码</label> 
        <a href="javascript:void(0)" @click="show">找回密码</a>
        <a href="javascript:void(0)" @click="show">注册</a>
      </div>
      <div class="login">
        <input type="submit" name="login" value="登 录" @click="login">
      </div>
      
      <div class="other-way">
        <h3>其他方式登录</h3>
        <li><a href="javascript:void(0)"><i class="icon-qq"></i>QQ登录</a></li>
        <li><a href="javascript:void(0)"><i class="icon-blog"></i>微博登录</a></li>
        <li><a href="javascript:void(0)"><i class="icon-wechat"></i>微信登录</a></li>
      </div>
    </div>
  </div>
  -->
</template>


<script>
  import{mapActions} from 'vuex'
  export default {
    name:'login',
    data(){
      return{
        forgetPassword:false,
        loading:false,
        loginLoading:false,
        
        user:{
          account:'',
          password:''
        },
        forgetForm: {
          email:''
        },
        rules:{
          account:[
            {required:true, message:'请输入用户名，默认：gentle', trigger:'blur'},
            { min: 4, max: 10, message: '用户名长度为4 到 10 个字符', trigger: 'blur' }
          ],
          password:[
            {required:true, message:'请输入密码，默认：2406',trigger:'blur'},
            { min: 4, max: 10, message: '密码长度为4 到 10 个字符', trigger: 'blur' }
          ],
          email:[
            {required:true,message:'请输入邮箱地',trigger:'blur'},
            {type:'email',message:'请输入正确的邮箱地址',trigger:['blur','change']}
          ]
        }
      }
    },
    method:{
      ...mapActions([
        'loginAction'//this.loginAction()映射为this.$store.dispatch('loginAction')
      ]),
      forget(){
        this.forgetPassword = true;
      },
      hideForgetPasswordDialog() {
        this.forgetPassword = false;
      },
      torRegister(){
        //忘记密码，注册
        this.$router.push({name:'register'});//跳转到注册页面
      },
      toLogin() {
        let _ts=this;
        _ts.$refs.user.validate((valid) =>{
          if (valid) {
            _ts.$set(_ts, 'loginLoading', true); //调用this.$set() 是为了响应式处理；因此没有直接用this.loginLoading=tue
            setTimeout(() => {
              _ts.$set(_ts,'loginLoading',false);
            }, 2000);
            _ts.axiox.$post('/login',_ts.user)
            .then(res => {
              _ts.$set(_ts,'loginLoading',false);
              if (res.data.code==200) {
                  _ts.data = res.data;
                  _ts.user.account='';
                  _ts.user.password='';
                  //如果存在查询参数
                  if (_ts.$route.query.redirect) {
                    let redirect = _ts.$route.query.redirect;
                    //跳转至进入登录页前的路由
                    _ts.$router.replace(redirect);
                  } else {
                    // 否则跳转至首页
                    _ts.$router.replace({name:'home'});
                  }
                } else if(res.data.code==500) {
                  _ts.$message.error('用户登录失败');
                } else if(res.data.code==400) {
                  _ts.$message.error('用户名或密码错误');
                }
            }).catch(error => {
              _ts.$message.error(error.message)
            })
            //表单验证失败
          } else {
            return false;
          }
        })
      },
      sendEmailCode(){
        let _ts = this;
        _ts.loading = true;
        let email = _ts.email;
        if (!email) {
          return false;
        }
        let data = _ts.forgetForm.email;
        _ts.axios.$get('/get-forget-password-email', {params:data})
        .then((res) => {
          _ts.loading = false;
          _ts.forgetPassword = false;
          if (res) {
            _ts.$message(res.message)
          }
        })
        .catch((error) => {
          _ts.$message.error(error.message)
        })
      }
    }
  }
</script>
<style src="@/assets/css/icon.css"></style>
<style>
  
.bg {
  position: absolute;
  top:50px;
  left:25%;
  width:50%;
  height: 70%;

}

.login-container {
  width:60%;
  margin:-14% -6% 4% 15%;
}

.login-title {
  font-size: 20px;
  color: #4d7c6e;
  width:100%;
  
  
}

.bottom-text {
  color: rgb(49, 49, 224);
  margin: 0 5px;
  float: right;
}

.other-way{
  width: 100%;
  max-width: 320px;
  color:rgb(87, 87, 87);
  margin: auto;
  font-size: 14px;
}



.other-way li {
  width: 33.333%;
  display: block;
  float: left;
}


/*  没有使用 elementUI之前的自写样式

.login-box{
  display: flex;
  align-items: center;
  height: 100%;
  padding: 10px 10px 15px;
}

h3.title{
  text-align: center;
  padding: 8px;
  font-weight: 500;
}

.clear{
  clear: both;
}

.login-form{
  max-width: 320px;
  background-color: aliceblue;
  margin: 20px auto;
}

.user-name, .user-pass, .user-email, .user-tel, .verification {
  position: relative;
  height: 42px;
  margin-bottom: 5px;
}

form label {
  position: absolute;
  display: block;
  width: 40px;
  height: 40px;
  line-height: 40px;
  color: black;
  background: #fff;
  text-align: center;
  top: 1px;
  left: 1px
}

form input {
  font-size: 14px;
  line-height: 18px;
  height: 42px;
  width: 100%;
  padding: 11px 8px 11px 50px;
  border: 1px solid #fff;
}

.note-pass{
  max-width: 320px;
  margin: 15px auto;
  font-size: 14px;
}

.note-pass a {
  color: blue;
  text-decoration: none;
  float: right;
}

.login{
  width: 100%;
  max-width: 320px;
  margin: 0 auto;
}

.login input {
  width: 100%;
  font-size: 14px;
  background-color: dodgerblue;
}

.other-way{
  width: 100%;
  max-width: 320px;
  margin: 20px auto;
  font-size: 14px;
}

.other-way li {
  width: 33.333%;
  display: block;
  float: left;
}

.other-way span {
  display: none;
}

@media screen and (min-width: 640px) {
  form, .login, .other-way {
    max-width: 640px;
    margin: 20px auto;
  }

  .other-way span {
    display: inline-block;
  }

  .login input {
    width: 100%;
    font-size: 16px;
  }
}

@media screen and (min-width: 1025px) {

  .login-box, .login-box2 {
    width: 100%;
    max-width: 360px;
    height: 430px;
    position: absolute;
    margin-top: 20px;
    margin-left: 0px;
    background: #f8f8f8;
    right: 2%;
    padding: 10px 20px;
  }

  .login-box2 {
    height: 430px;
  }
}


*/
</style>

