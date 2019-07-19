<template>
  <v-app>
    <v-content>
      <DashBoard/>
    </v-content>
  </v-app>
</template>

<script>
import HelloWorld from './components/HelloWorld'
import DashBoard from './components/DashBoard'

export default {
  name: 'App',
  components: {
    DashBoard
  },
  data () {
    return {
      //
    }
  }
}
</script>
