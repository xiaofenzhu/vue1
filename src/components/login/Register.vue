<template>
  <div class="ma">
    <table class="bg" bgcolor="#d3d3d3">
      <div class="mb">
        <span style="margin-top: 50px">注册</span>
      </div>
      <div>
        <el-form ref="form" :model="student" :rules="rules" label-width="40px" >
          <el-form-item prop="name">
            <el-input prefix-icon="el-icon-user" v-model="student.name" placeholder="请输入姓名" ></el-input>
          </el-form-item>
          <el-form-item prop="password">
            <el-input prefix-icon="el-icon-lock" v-model="student.password" clearable size="small" type="password" placeholder="请输入6-20位密码,数字,字母和符号组合"></el-input>
          </el-form-item>
          <el-form-item prop="confirmPwd" class="mc">
            <el-input prefix-icon="el-icon-lock" v-model="student.confirmPwd" clearable size="small" type="password" placeholder="请再次输入上方密码"></el-input>
          </el-form-item>
          <el-form-item class="mc">
            <el-radio-group v-model="student.sex">
              <el-radio label="男"  value="男"></el-radio>
              <el-radio label="女"  value="女"></el-radio>
            </el-radio-group>
          </el-form-item>
          <el-form-item prop="idcard">
            <el-input prefix-icon="el-icon-user" v-model="student.idcard" placeholder="请输入身份证号" onkeyup="value=value.replace(/[^\d]/g,'')"></el-input>
          </el-form-item>
          <el-form-item prop="stuid">
            <el-input prefix-icon="el-icon-user" v-model="student.stuid" placeholder="请输入学号" onkeyup="value=value.replace(/[^\d]/g,'')"></el-input>
          </el-form-item>

          <el-form-item label="院系" :label-width="formLabelWidth">
            <el-select v-model="student.did" placeholder="请选择部门">
              <el-option v-for="item in depts" :key="item.id" :label="item.name" :value="item.id" ></el-option>
            </el-select>
          </el-form-item>

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
          <el-form-item  :label-width="formLabelWidth" prop="photo">
            <img v-if="imgurl!=''" :src="imgurl" width="100">
            <el-upload
              class="upload-demo"
              action=""
              :auto-upload="false"
              :on-change="handleChange"
              :file-list="fileList">
              <el-button size="small" style="margin-left: 160px" type="primary">点击上传</el-button>
              <div slot="tip" class="el-upload__tip" style="margin-left: 80px">只能上传jpg/png文件，且不超过500kb</div>
            </el-upload>
          </el-form-item>
          <el-form-item label-width="0">
            <el-button type="primary" @click="save" style="margin-left: 245px">注册</el-button>
          </el-form-item>
        </el-form>
      </div>
      <div>
        <span @click="dl" style="margin-left: 245px">去登陆></span>
      </div>
    </table>
  </div>
</template>

<script>
export default {
  name: 'Register',
  data () {
    var validatePass = (rule, value, callback) => {
      if (value === '') {
        callback(new Error('请输入密码'))
      } else {
        if (this.student.confirmPwd !== '') {
          this.$refs.form.validateField('confirmPwd')
        }
        callback()
      }
    }
    var validatePass2 = (rule, value, callback) => {
      if (value === '') {
        callback(new Error('请再次输入密码'))
      } else if (value !== this.student.password) {
        callback(new Error('两次输入密码不一致!'))
      } else {
        callback()
      }
    }
    var validateCode = (rule, value, callback) => {
      if (value === (this.code)) {
        callback()
      } else {
        callback(new Error('验证码输入错误'))
      }
    }
    return {
      formLabelWidth: '80px',
      imgurl: '',
      fileList: [],
      code: '',
      file: [],
      depts: [],
      student: {
        password: '',
        confirmPwd: '',
        code: '',
        sex: ''
      },
      rules: {
        name: [
          {required: true, message: '请输入姓名', trigger: 'blur'},
          // eslint-disable-next-line standard/object-curly-even-spacing
          { min: 3, max: 10, message: '姓名必须是3到10位', trigger: 'blur'}
        ],
        password: [
          {required: true, message: '请输入6-20位密码,数字,字母和符号组合', trigger: 'blur'},
          {min: 6, max: 20, message: '密码输入格式错误', trigger: 'blur'},
          {validator: validatePass, trigger: 'blur'}
        ],
        confirmPwd: [
          {required: true, message: '请再次输入上方密码', trigger: 'blur'},
          {min: 6, max: 20, message: '两次密码输入不一致，请重新输入', trigger: 'blur'},
          {validator: validatePass2, trigger: 'blur', required: true}
        ],
        idcard: [
          {required: true, message: '请输入身份证号', trigger: 'blur'},
          // eslint-disable-next-line standard/object-curly-even-spacing
          { min: 6, max: 6, message: '身份证号必须是6位数字', trigger: 'blur'}
        ],
        stuid: [
          {required: true, message: '请输入学号', trigger: 'blur'},
          // eslint-disable-next-line standard/object-curly-even-spacing
          { min: 10, max: 10, message: '学号必须是10位数字', trigger: 'blur'}
        ],
        phone: [
          {required: true, message: '请输入手机号', trigger: 'blur'},
          // eslint-disable-next-line standard/object-curly-even-spacing
          { min: 11, max: 11, message: '手机号必须是11位数字', trigger: 'blur'}
        ],
        code: [
          {required: true, message: '请输入验证码', trigger: 'blur'},
          {min: 6, max: 6, message: '验证码输入错误', required: true},
          {validator: validateCode, trigger: 'blur', required: true}
        ]
      }
    }
  },
  methods: {
    handleChange (file) {
      this.fileList[0] = file
      this.imgurl = URL.createObjectURL(file.raw)
    },
    save () {
      this.$refs.form.validate(valid => {
        if (valid) {
          var fd = new FormData()
          Object.keys(this.student).forEach(key => {
            fd.append(key, this.student[key])
          })
          fd.append('photo', this.fileList[0].raw)
          this.$axios.post(
            '/student/register', fd
          ).then(resp => {
            alert(resp.data)
            this.$router.push('/Login')
          })
        }
      })
    },
    dl () {
      this.$router.push('Login')
    },
    getDepts () {
      this.$axios.get('dept/findAll')
        .then(resp => {
          this.depts = resp.data
        })
    },
    yzm () {
      this.$axios.get('/student/getCode')
        .then(resp => {
          alert(resp.data)
          this.code = resp.data
          console.log(this.code)
        })
    }
  },
  created () {
    this.getDepts()
  }
}
</script>

<style scoped>

.ma{
  width: 100%;
  height: 1500px;
  background: url("../../assets/logo.png");
  position: absolute;
  background-size: 100%;
}
.bg{
  width: 30%;
  height: 80%;
  position: relative;
  left: 35%;
  bottom: 40%;
  top: 6%;
}
.mb{
  display: flex;
  justify-content: space-around;
  height: 100px;
}
.mc{
  height: 30px;
}
</style>
