<template>
            <v-col dark class="text-center" cols="12">
              <h1 class="display-2 font-weight-light mb-4 text-h1">Your Finance</h1>
              <h4 class="subheading">Keep your payments in order</h4>
              <v-btn to="/payments">Payments</v-btn> |
              <v-btn to="/login">Login</v-btn>
            </v-col>
</template>

<script>
  export default {
    name: 'Home',
    components: {
    },
    mounted() {

    }
  }
</script>
