<template>
  <div class="register">
    <section class="form_container">
      <div class="manage_tip">
        <span class="title">欢迎使用后台管理系统</span>
        <span class="sub_title">ElementUI 是大中华区最具影响力的 Web 设计规范</span>
        <el-form
          :model="loginUser"
          :rules="rules"
          ref="loginForm"
          label-width="80px"
          class="loginForm"
        >
          <el-form-item label="邮箱" prop="email">
            <el-input type="email" v-model="loginUser.email" placeholder="请输入Email"></el-input>
          </el-form-item>
          <el-form-item label="密码" prop="password">
            <el-input type="password" v-model="loginUser.password" placeholder="请输入密码"></el-input>
          </el-form-item>
          <el-form-item label="选择身份" prop="identity">
            <el-select v-model="loginUser.identity" placeholder="请选择身份">
              <el-option label="管理员" value="manager"></el-option>
              <el-option label="员工" value="employee"></el-option>
            </el-select>
          </el-form-item>
          <el-form-item>
            <el-button type="primary" class="submit_btn" @click="submitForm('loginForm')">登录</el-button>
            <div class="tiparea">
                <p>还没有账号？现在 <router-link to="/register">注册</router-link> </p>
            </div>
          </el-form-item>
        </el-form>
      </div>
    </section>
  </div>
</template>
<script>
import jwt_decode from 'jwt-decode'
export default {
    name:'login',
  data() {
   
    return {
      loginUser: {
        name: "",
        email: "",
        password: "",
        password2: "",
        identity: ""
      },
      rules: {
        email: [
          {
            required: true,
            trigger: "blur",
            message: "邮箱格式不正确"
          }
        ],
        password: [
          { required: true, trigger: "blur", message: "密码不能为空" },
          {
            required: true,
            min: 6,
            max: 30,
            message: "长度在6到30个字符之间",
            trigger: "blur"
          }
        ]
      }
    };
  },
  methods: {
    submitForm(formName) {
      this.$refs[formName].validate(valid => {
        if (valid) {
          this.$axios.post("/api/users/login", this.loginUser).then(res => {
              // token
              const {token} = res.data;
              // 存储到localstorage
              localStorage.setItem('eleToken',token);
              // 解析token
              const decoded =jwt_decode(token)
              this.$store.dispatch("setAuthentivated",!this.isEmpty(decoded))
              this.$store.dispatch("SetUser",decoded)
              // console.log(decoded)
          this.$router.push("/index");
          });
        }
      });
    },
    // 判空方法
    isEmpty(value){
      return (
        value === undefined ||
        value === null || 
        (typeof vaule === "object" && Object.keys(value).length ===0 ) || 
        (typeof vaule === "string" && value.trim().length ===0 )
      )
    },
    resetForm(formName) {
      this.$refs[formName].resetFields();
    }
  }
};
</script>
<style >
.sub_title{
  display: block;
  color: #a9b5c4;
  font-size: 14px;
  margin-top: 10px;
}
.register {
  position: relative;
  width: 100%;
  height: 100%;
  background-color: #2d3a4b;
}
.form_container {
  width: 370px;
  height: 210px;
  position: absolute;
  top: 10%;
  left: 34%;
  padding: 25px;
  border-radius: 5px;
  text-align: center;
}
.form_container .manage_tip .title {
  font-family: "Microsoft YaHei";
  font-weight: bold;
  font-size: 26px;
  color: #fff;
}
.loginForm {
  margin-top: 20px;
  background-color: #fff;
  padding: 20px 40px 20px 20px;
  border-radius: 5px;
  box-shadow: 0px 5px 10px #cccc;
}
.tiparea {
    text-align: right;
    font-size: 12px;
    color: #333;
}
.tiparea p a{
  color: #409eff
}
.submit_btn {
  width: 100%;
}
</style>

