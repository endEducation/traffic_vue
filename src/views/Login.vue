<template>

<div>

  <div class="LoginBox" v-if="status == 1">
	 <el-card class="box-card">
	<p class="login_header">登录页面</p>
    <div class="LoginBox-Box">
       <el-form :model="form" ref="form" :rules="rules" label-width="0" :inline="false" size="normal" key="1">
      <el-form-item prop="username">
        <el-input v-model="form.username"></el-input>
      </el-form-item>
       <el-form-item prop="password">
        <el-input type="password" v-model="form.password" @keyup.enter.native="onSubmit"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button class="login iconfont My-new-iconxuanzhonghaoyou" type="primary" v-bind:disabled="able" @click="onSubmit">登录</el-button>
      </el-form-item>
      <el-form-item>
        <span><span class="sign fl" @click="sign">去注册</span></span>
		<span><span class="reset" @click="resetpassword">忘记密码？点这里</span></span>
        <span><span class="sign fr" @click="Gohome">去主页</span></span>
      </el-form-item>
    </el-form>
    </div>
	</el-card>
  </div>

  <div class="register"  v-else>
  <el-card class="box-card">
  <p class="login_header">注册页面</p>
    <div class="registerBox">
      <el-form :model="regform" ref="regform" :rules="regrules" label-width="0" :inline="false" size="normal" key="2">
      <el-form-item prop="nickname">
        <el-input v-model="regform.nickname" placeholder="请输入昵称"></el-input>
      </el-form-item>
      <el-form-item  prop="pass">
        <el-input type="password" placeholder="请输入密码" v-model="regform.pass" autocomplete="off"></el-input>
      </el-form-item>
      <el-form-item  prop="checkPass">
        <el-input type="password" placeholder="请再次输入密码" v-model="regform.checkPass" autocomplete="off"></el-input>
      </el-form-item>
	  <el-form-item prop="gender">
		<!-- <el-select v-model="regform.gender" placeholder="请选择性别" style="width: 100%;">
			<el-option label="男" value="0"></el-option>
			<el-option label="女" value="1"></el-option>
		</el-select> -->
	  </el-form-item>
      <el-form-item>
        <el-button class="login" type="primary" :disabled="registerAble" @click="Register">注册</el-button>
      </el-form-item>
      <el-form-item>
        <p><span class="sign fl" @click="Gotologin">去登录</span></p>
        <span><span class="sign fr" @click="Gohome">去主页</span></span>
      </el-form-item>
    </el-form>
    </div>
    </el-card>
  </div>
  <!-- 全屏等待加载 -->
  <div v-loading.fullscreen.lock="loading"></div>
  <!-- <el-dialog
    title="验证"
    :visible.sync="centerDialogVisible"
	width="30%"
    center>
    <verify @success="onVerifySuccess" @error="onVerifyError" :imgUrl="imgurl" :imgName="imgname"  type="4" :showButton="false"></verify>
  </el-dialog>
  <el-dialog
    title="验证"
    :visible.sync="registerDialogVisible"
  	width="30%"
    center>
    <verify @success="registerSuccess" @error="registerError" :imgUrl="imgurl" :imgName="imgname"  type="5" :showButton="false"></verify>
  </el-dialog> -->
</div>
</template>

<script>
  import Cookie from 'js-cookie'
  import Verify from 'vue2-verify'
  import { showLoading, hideLoading } from '../components/loading.js';
  export default {
	components: {
		Verify,
	},
    data() {
     // 确认密码的规则
         var validatePass = (rule, value, callback) => {
        if (value === '') {
          callback(new Error('请输入密码'));
        } else if(value.length < 3) {
			callback(new Error('密码位数在3~15之间'))
		} else {
          if (this.regform.checkPass !== '') {
            this.$refs.regform.validateField('checkPass');
          }
          callback();
        }
      };
      var validatePass2 = (rule, value, callback) => {
        if (value === '') {
          callback(new Error('请再次输入密码'));
        } else if (value !== this.regform.pass) {
          callback(new Error('两次输入密码不一致!'));
        } else {
          callback();
        }
      };

      return {
        loading:false,
        form:{
          username:'',
          password:'',
        },
        regform:{
          gender:'',
          nickname:'',
          pass:'',
          checkPass:''
        },
		imgurl: "http://jximg.iboy.tech/verify/",
		imgname: ['1.jpg','2.jpg','3.jpg','4.jpg'],
		centerDialogVisible: false,
		registerDialogVisible: false,
		textSlider: '滑动完成验证',
		registerAble: false,
        status:1,
		able: false,
         rules: {
          username: [
            { required: true, message: '请输入用户名', trigger: 'blur' },
            { min: 3, max: 15, message: '长度在 3 到 15 个字符', trigger: 'blur' }
          ],
          password: [
            { required: true, message: '请输入密码', trigger: 'blur' },
            { min: 3, max: 15, message: '长度在 3 到 15 个字符', trigger: 'blur' }
          ],
        },

         regrules: {
          // gender: [
          //   { required: true, message: '请选择性别', trigger: 'change' }
          // ],
          nickname:[
             { required: true, message: '请输入您的昵称', trigger: 'blur' },
            { min: 3, max: 15, message: '长度在 3 到 15 个字符', trigger: 'blur' }
          ],
          pass: [
            { validator: validatePass, trigger: 'blur' },
          ],
          checkPass: [
            { validator: validatePass2, trigger: 'blur' }
          ]
        }

      }
    },
    methods:{
		// 验证码验证成功的回调函数
	  onVerifySuccess(obj) {
		  obj.refresh();
		  this.centerDialogVisible = false;
		  this.login();
		  this.able = true
	  },
	  // 注册验证成功回调函数
	  registerSuccess(obj) {
		  obj.refresh();
		  this.registerDialogVisible = false;
		  this.Register()
		  this.registerAble = true
	  },
	  Register() {
		  // showLoading()
		  let data = {"nickname":this.regform.nickname,"password":this.regform.pass};
		  console.log(data)
		  this.$http.post('/register',data)
		  .then(res=>{
			  hideLoading()
		  	console.log("返回的信息：")
		  	console.log(res)
		      if(res.data.code === -1 ){
		         this.$message({
		          message: res.data.msg,
		          type: 'warning'
		        });
		      }else {
		          this.$message({
		          message: '注册成功',
		          type: 'success'
		        });
		        this.status = 1;
		      }
		  })
	  },
      // 注册
      register(){
		this.$refs['regform'].validate((valid) => {
			if(valid) {
				this.registerDialogVisible = true
			} else {
				this.$message.error("请输入正确信息")
			}
		})
      },
      // 去主页
      Gohome(){
        this.$router.push({name:'home'})
      },

    Gotologin(){
      this.status = 1;
    },
    sign(){
        this.status = 2;
    },
	// 登录函数
	login() {
		// showLoading()
		console.log(this.form)
		 this.$http.post('/login',this.form)
		 .then(res=>{
			 hideLoading()
		  console.log(res);
		  if(res.data.code === 200){
			this.$message.success(res.data.msg)
		    // 登录成功，去vuex里修改登录状态
		    this.$store.commit('changIsSignIn',1)
		    // 设置Cookie
		    Cookie.set('token',res.data.data)
		    // 设置vuex里的token
		    this.$store.commit('setToken',res.data.data)
		    this.$router.push({name:'home'})
		    setTimeout(() => {
		      this.loading = false
		    },1000)
		  } else {
			  this.able = false
			  this.$message.error(res.data.msg)
		  }
		  })
	},
    // 登录
    onSubmit(){
        this.$refs['form'].validate((res) => {
          if(res){
            // this.loading = true;
            // 发起登录请求
            this.login();
			// this.centerDialogVisible = true;
          }else {
            this.$message.error("请输入正确信息")
            return false
          }
        })
      },
	  // 忘记密码函数
	  resetpassword() {
		  this.$prompt('请输入昵称','提示',{
			  confirmButtonText: '确定',
			  cancelButtonText: '取消',
			  inputValidator: (value)=>{
					if(value === '') return '请输入昵称'
					else if (value.length < 3 || value.length > 15) return '昵称长度在3~15之间'
			  }
		  }).then(({value}) => {
			  showLoading();
			  this.$http.post('/user/resetPass',{
				  'nickname': value
			  }).then(res => {
				  if(res.data.code === 200) {
					  this.$message({
						  type: 'success',
						  message: res.data.msg,
						  offset: 80
					  })
					  this.$notify({
						  type: 'success',
						  title: '恭喜，初始化成功！',
						  message: '密码已初始化为123456',
						  duration: 0
					  })
				  } else {
					  this.$message({
						  type: 'error',
						  message: res.data.msg,
						  offset: 80
					  })
				  }
				  hideLoading();
			  })
		  }).catch(() => {
			  // 取消操作
		  });
	  }
    }
  }
</script>

<style lang="scss" scoped>
.register,
.LoginBox {
  width: 400px;
  height: 555px;
  position: absolute;
  left: 50%;
  transform: translateX(-50%);
  top: 20%;
  padding: 15px;
  .registerBox,
  .LoginBox-Box {
    margin-top: 10%;
  }
  .login {
    width: 100%;
  }
  .sign {
    font-size: 18px;
    color: #dfe7ef;
    font-weight: bold;
    margin-right: 15px;
	cursor: pointer;
  }
  .fl {
	  float: left;
  }
  .fr {
	  float: right;
  }
  .reset {
	  font-size: 12px;
	  color: #00193A;
	  cursor: pointer;
	  text-align: center;
  }
}
.login_header {
	font-size: 28px;
	font-weight: bold;
	font-family: "microsoft sans serif";
	text-align: center;
	margin-top: 10%;
	color: #42c7f3;
}
.box-card {
	background-color: rgba(128,202,169,0.6);
	border: none;
}
.el-input--normal {
  position: relative;
  overflow: hidden;
}
.el-input--normal::before {
    content: '';
    position: absolute;
    left: 0;
    height: 3px;
    bottom: 0;
    width: 100%;
    background: linear-gradient(to right,white,#3498db,#1abc9c,
    #8e44ad,#e67e22,#e74c,white);
    transform: translateX(-100%);
    transition: 1s;
  }
  .el-input--normal:hover::before {
    transform: translateX(100%);
  }

</style>
