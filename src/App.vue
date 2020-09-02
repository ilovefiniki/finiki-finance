<template>
  <v-app>
    <v-app-bar
      app
      color="primary"
      dark
      absolute
    >
      <v-btn icon tile to="/">
        <v-icon>mdi-home</v-icon>
      </v-btn>
      <v-toolbar-title>Finiki Finance</v-toolbar-title>
      <v-spacer></v-spacer>
      <v-btn icon tile to="/login">
        <v-icon>mdi-login</v-icon>
      </v-btn>
      <v-btn icon tile to="/logout">
        <v-icon>mdi-logout</v-icon>
      </v-btn>
    </v-app-bar>

    <v-main>
      <v-img
              class="fill-height"
              src="https://picsum.photos/id/1025/1920/1080"
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
<script>
export default {
  name: 'App',
  data: () => ({
    //
  }),
};
</script>
