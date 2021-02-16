<template>
    <v-app-bar
            app
            color="blue-grey darken-3"
            dark
            absolute
    >
        <v-btn icon tile to="/">
            <v-icon>mdi-home</v-icon>
        </v-btn>
        <v-toolbar-title>
            Finiki Finance
        </v-toolbar-title>
        <v-spacer></v-spacer>
        <v-btn v-if="currentUser" icon tile to="/logout">
            <v-icon>mdi-logout</v-icon>
        </v-btn>
        <v-btn v-else
                icon tile to="/login">
            <v-icon>mdi-login</v-icon>
        </v-btn>
    </v-app-bar>
</template>

<script>
   import * as firebase from 'firebase/app'
    import 'firebase/auth'
    import 'firebase/database';

    export default {
        data() {
            return {
                currentUser: {},
            }
        },
        mounted() {
            this.checkUserSession()
        },

        filters: {

        },
        methods: {
            async checkUserSession() {
                try{
                    this.currentUser = await firebase.auth().currentUser
                } catch (e) {
                    console.log(e.message)
                    this.loading = false
                }
            },

        },
        components: {
        }
    }
</script>