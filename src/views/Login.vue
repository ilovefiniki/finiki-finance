<template>
        <v-card class="ma-3 pa-6">
            <v-card-title>Login</v-card-title>
            <v-card-text>
                <v-form
                        ref="loginForm"
                        v-model="valid"
                        lazy-validation
                >
                    <v-text-field
                            label="Email"
                            required
                            outlined
                            type="email"
                            v-model="email"
                            :rules="emailRules"
                    ></v-text-field>
                    <v-text-field
                            label="Password"
                            requiered
                            outlined
                            type="password"
                            v-model="password"
                            :rules="passwordRules"
                    ></v-text-field>
                    <v-btn
                            :disabled="!valid"
                            @click="login">Login</v-btn>
                </v-form>
            </v-card-text>
        </v-card>

</template>
<script>
    import * as firebase from 'firebase/app'
    import 'firebase/auth'

export default {
    data() {
        return {
            valid: true,
            email: 'ilovefiniki@gmail.com',
            emailRules: [
                v => !!v || 'E-mail is required',
                v => /.+@.+\..+/.test(v) || 'E-mail must be valid',
            ],
            password: '',
            passwordRules: [
                v => !!v || 'Pass is required'
            ],
            error: ''
        }
    },
    methods: {
        async login() {
            if(this.$refs.loginForm.validate()) {
                try {
                    await firebase.auth().signInWithEmailAndPassword(this.email, this.password)
                    this.$router.push('/payments')
                } catch (err) {
                    console.log(err)
                    throw err
                }
            }
        },
        async logout() {
            try {
                await firebase.auth().signOut()
                this.$router.push('/login')
            } catch (err) {
                console.log(err)
                throw err
            }
        }
    }
}
</script>