<template>
  <div class="login">
    <img alt="kakaobank logo" src="../assets/logo.png">
    <Login/>
  </div>
</template>

<script>
// @ is an alias to /src
import Login from '@/components/Login.vue'

export default {
  name: 'LoginView',
  components: {
    Login
  }
}
</script>
