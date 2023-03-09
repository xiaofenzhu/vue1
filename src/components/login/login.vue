<template>
  <div>
    <div class="ma">
      <table class="bg" bgcolor="#d3d3d3">
        <div class="mb">
          <span style="margin-top: 50px">登录</span>
        </div>
        <div>
          <el-tabs v-model="xuan" stretch="stretch" @tab-click="handleClick" style="margin-top: 50px" >
            <el-tab-pane label="验证码登陆" name="first">
              <el-form ref="form1" :model="student" :rules="rules1" label-width="40px" >
                <el-form-item prop="phone">
                  <el-input prefix-icon="el-icon-user" v-model="student.phone" placeholder="请输入手机号" onkeyup="value=value.replace(/[^\d]/g,'')"></el-input>
                </el-form-item>
                <el-form-item prop="code" label-width="40px">
                  <el-col :span="12">
                    <el-input prefix-icon="el-icon-chat-dot-round" v-model="student.code" placeholder="请输入验证码" onkeyup="value=value.replace(/[^\d]/g,'')"></el-input>
                  </el-col>
                  <el-col :span="2"></el-col>
                  <el-col :span="10" >
                    <el-button type="primary" @click="yzm">获取验证码</el-button>
                  </el-col>
                </el-form-item>
                <br>
                <br>
                <br>
                <el-form-item label-width="0%">
                  <el-button type="primary" @click="save1" style="margin-left: 140px;width: 50%">登录</el-button>
                </el-form-item>
              </el-form>
            </el-tab-pane>
            <el-tab-pane label="密码登录" name="second" >
              <el-form ref="form2" :model="student" :rules="rules2" label-width="40px" >
                <el-form-item prop="phone" style="left: 10px">
                  <el-input prefix-icon="el-icon-user" v-model="student.phone" placeholder="请输入手机号" onkeyup="value=value.replace(/[^\d]/g,'')"></el-input>
                </el-form-item>
                <el-form-item prop="password">
                  <el-input prefix-icon="el-icon-lock" v-model="student.password" type="password" placeholder="请输入密码"></el-input>
                </el-form-item>
                <br>
                <br>
                <br>
                <el-form-item label-width="0%">
                  <el-button type="primary" @click="save2" style="margin-left: 140px;width: 50%">登录</el-button>
                </el-form-item>
              </el-form>
            </el-tab-pane>
            <span style="display: flex;justify-content: center">
              <span style="align-content: center" @click="zc" class="shou">去注册></span>
            </span>
          </el-tabs>
        </div>
      </table>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Login',
  data () {
    var validateCode = (rule, value, callback) => {
      if (value === this.codes && value.length === 6) {
        callback()
      } else {
        callback(new Error('验证码输入错误'))
      }
    }
    return {
      phone: '',
      codes: '',
      xuan: 'second',
      student: {
        password: '',
        code: ''
      },
      rules1: {
        phone: [
          {required: true, message: '请输入手机号', trigger: 'blur'},
          // eslint-disable-next-line standard/object-curly-even-spacing
          { min: 11, max: 11, message: '手机号必须是11位数字', trigger: 'blur'}
        ],
        code: [
          // {required:true,message:'请输入验证码',trigger:"blur"},
          // {min:6,max:6,message: "验证码输入错误",required: true},
          // {validator:validateCode, trigger: 'blur' ,required: true}
          {required: true, validator: validateCode, trigger: 'blur'}
        ]
      },
      rules2: {
        phone: [
          {required: true, message: '请输入手机号', trigger: 'blur'},
          // eslint-disable-next-line standard/object-curly-even-spacing
          { min: 11, max: 11, message: '手机号必须是11位数字', trigger: 'blur'}
        ],
        password: [
          {required: true, message: '请输入6-20位密码,数字,字母和符号组合', trigger: 'blur'},
          {min: 6, max: 20, message: '密码输入格式错误', trigger: 'blur'}
        ]
      }
    }
  },
  methods: {
    zc () {
      this.$router.push('Register')
    },
    handleClick (tab, event) {
      console.log(tab, event)
    },
    yzm () {
      this.$axios.get('/student/getCode')
        .then(resp => {
          alert(resp.data)
          this.codes = resp.data
          console.log(this.codes)
        })
    },
    save1 () {
      this.$refs.form1.validate(valid => {
        if (valid) {
          var fd = new FormData()
          Object.keys(this.student).forEach(key => {
            fd.append(key, this.student[key])
          })
          this.$axios.post(
            '/student/login1', fd
          ).then(resp => {
            var data = resp.data
            if (data.code !== '0') {
              alert('手机号不存在')
            } else {
              alert('登陆成功')
              var s = JSON.stringify(data.msg)
              console.log(data.msg)
              localStorage.setItem('token', s)
              this.$router.push('/Sindex')
            }
          })
        }
      })
    },
    save2 () {
      this.$refs.form2.validate(valid => {
        if (valid) {
          var fd = new FormData()
          Object.keys(this.student).forEach(key => {
            fd.append(key, this.student[key])
          })
          this.$axios.post(
            '/student/login2', fd
          ).then(resp => {
            var data = resp.data
            if (data.code !== '0') {
              alert(data.msg)
            } else {
              alert('登陆成功')
              var s = JSON.stringify(data.msg)
              console.log(data.msg)
              localStorage.setItem('token', s)

              this.$router.push('/Sindex')
            }
          })
        }
      })
    }
  }
}
</script>

<style scoped>
.ma{
  width: 100%;
  height: 100%;
  background: url("../../assets/logo.png");
  position: absolute;
  background-size: 100%;
}
.bg{
  width: 30%;
  height: 60%;
  position: relative;
  left: 35%;
  bottom: 20%;
  top: 20%;
}
.mb{
  display: flex;
  justify-content: space-around;
  height: 100px;
}
</style>
