<template>
  <div class="login">
    <ul>You must log in to access this service.</ul>
    <ul>
      <li><el-input placeholder="Id" v-model="inputId"></el-input></li>
    </ul>
    <ul>
      <li><el-input placeholder="Password" v-model="inputPassword" show-password></el-input></li>
    </ul>
    <ul>
      <li><el-button id="kakao-login-btn" type="primary" style="width:450px;background:#ffcc00;border-color:#ffcc00" v-on:click="onLogin">LOGIN</el-button></li>
    </ul>
    <ul>
      <li><el-button type="primary" style="width:450px;background:#ffcc00;border-color:#ffcc00" v-on:click="onSignup">SIGNUP</el-button></li>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'Login',
  props: {
    msg: String
  },
  methods: {
    onValidation: function () {
      if (!this.inputId === '' || this.inputPassword === '') {
        this.$message.error('아이디와 패스워드 중 하나 이상의 값이 비어있습니다.')

        return false
      }
      return true
    },
    onLogin: function () {
      if (this.onValidation()) {
        let _reqBody = {}
        _reqBody.id = this.inputId
        _reqBody.password = this.inputPassword

        const _baseURI = 'http://localhost:9090/user/login/'

        this.$http.post(`${_baseURI}`, _reqBody).then(response => {
          if (response.status === 200) {
            if (response.data === true) {
              this.$router.push({ name: 'homeView', params: { id: this.inputId } })
              this.$message({
                showClose: true,
                message: '로그인되었습니다.',
                type: 'success'
              })

              this.inputId = ''
              this.inputPassword = ''
            } else {
              this.$message({
                showClose: true,
                message: '로그인 정보가 잘못되었습니다.',
                type: 'warning'
              })
            }
          }
        })
      }
    },
    onSignup: function () {
      if (this.onValidation()) {
        let _reqBody = {}
        _reqBody.id = this.inputId
        _reqBody.password = this.inputPassword

        const _baseURI = 'http://localhost:9090/user/signup/'
        this.$http.post(`${_baseURI}`, _reqBody).then(response => {
          if (response.status === 200) {
            if (response.data === true) {
              this.$message({
                showClose: true,
                message: '가입이 완료되었습니다.',
                type: 'success'
              })

              this.inputId = ''
              this.inputPassword = ''
            } else {
              this.$message({
                showClose: true,
                message: '이미 가입이 되어있는 아이디입니다.',
                type: 'warning'
              })
            }
          }
        })
      }
    }
  },
  data () {
    return {
      inputId: '',
      inputPassword: ''
    }
  }
}
</script>

<style scoped lang="stylus">
h3
  margin 40px 0 0

ul
  list-style-type none
  padding 0

li
  display inline-block
  margin 0 10px
  width 450px

a
  color #42b983
</style>
