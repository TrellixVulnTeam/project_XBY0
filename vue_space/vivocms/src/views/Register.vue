<template>
  <div class="login">
    <h2>用户注册</h2>
    <el-form
      ref="form"
      :model="form"
      :rules="rules"
      status-icon

      label-width="80px"
    >
      <el-form-item label="账号" prop="username">
        <el-input v-model.trim="form.username"  />
      </el-form-item>
      <el-form-item label="密码" prop="password" >
        <el-input type="password" v-model.trim="form.password" show-password  />
      </el-form-item>
      <el-form-item label="确认密码" prop="chkpassword">
        <el-input type="password" v-model.trim="form.chkpassword" show-password  />
      </el-form-item>
      <el-form-item label="手机" prop="mobile">
        <el-input v-model.trim="form.mobile"  />
      </el-form-item>

      <el-form-item>
        <el-button type="primary" @click="submit">注册</el-button>
        <el-button type="warning" @click="$router.push('/login')"
          >返回</el-button
        >
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
import md5 from 'js-md5'
export default {
  data () {
    const checkUsername = (rule, value, callback) => {
      if (!value) {
        return callback(new Error('请输入用户名'))
      } else if (!/^[a-zA-Z]\w{3,15}$/.test(value)) {
        return callback(new Error('用户名必须以字母开头，4-15位'))
      }
      callback()
    }

    const checkPassword = (rule, value, callback) => {
      if (!value) {
        return callback(new Error('请输入密码'))
      } else if (!/^\w{6,}$/.test(value)) {
        return callback(new Error('密码至少六位'))
      }
      callback()
    }
    const checkchkPassword = (rule, value, callback) => {
      if (!value) {
        return callback(new Error('请输入确认密码'))
      } else if (value !== this.form.password) {
        return callback(new Error('两次密码输入不一致'))
      }
      callback()
    }

    const checkMobile = (rule, value, callback) => {
      if (!value) {
        return callback(new Error('请输入手机号码'))
      } else if (!/^1\d{10}$/.test(value)) {
        return callback(new Error('请输入有效的手机号码'))
      }
      callback()
    }
    return {
      form: {},
      rules: {
        username: { validator: checkUsername },
        password: { validator: checkPassword },
        chkpassword: { validator: checkchkPassword },
        mobile: { validator: checkMobile }
      }
    }
  },
  methods: {
    submit () {
      this.$refs.form.validate(valid => {
        if (valid) {
          this.$http.post('admin/create', {
            username: this.form.username,
            password: md5(this.form.password),
            mobile: this.form.mobile

          }).then(res => {
            if (res.result.err > 0) {
              this.$message.error(res.result.msg)
            } else {
              this.$message.success(res.result.msg)
              setTimeout(() => this.$router.push('/login'), 100)
            }
          })
        }
      })
    }
  }
}
</script>

<style lang="less">
.login {
  position: fixed;
  width: 360px;
  border-radius: 4px;
  box-shadow: 0 0 6px rgba(0, 0, 0, 0.3);
  padding: 20px;
  left: 50%;
  top: 15%;
  margin-left: -150px;
  h2 {
    padding: 20px 0;
    font-size: 24px;
    text-align: center;
  }
}
</style>
