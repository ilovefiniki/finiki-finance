<template>
  <v-app>

<Header/>

    <v-main>
      <v-img
              class="fill-height"
              src="bg.jpg"
      >
        <v-row
                align="center"
                justify="center"
                style="height:100%;"
        >
          <router-view/>
        </v-row>
      </v-img>
    </v-main>
  </v-app>
</template>
<style>
  .v-responsive__content {
    background: #ffffff38;
  }
</style>
<script>
  import Header from '@/components/Header'

export default {
  name: 'App',
  data: () => ({
    //
  }),
  components: {
    Header
  }
};
</script>
