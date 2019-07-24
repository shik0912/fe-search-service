<template>
  <div class="home">
    <Home v-bind:id="inputId"/>
  </div>
</template>

<script>
// @ is an alias to /src
import Home from '@/components/Home.vue'

export default {
  name: 'HomeView',
  components: {
    Home
  },
  data: function () {
    return {
      inputId: this.$route.params.id
    }
  }
}
</script>
