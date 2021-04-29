<template>
    <v-dialog
            v-model="dialog"
            width="500"
    >
        <template v-slot:activator="{ on, attrs }">
            <v-btn
                    v-bind="attrs"
                    v-on="on"
                    icon
            >
                <v-icon>mdi-pencil</v-icon>
            </v-btn>
        </template>

        <v-card class="pa-6">
            <div class="text-right">
                <v-btn icon @click="dialog = false">
                    <v-icon>mdi-close</v-icon>
                </v-btn>
            </div>
            <v-card-title>
                Edit Payment
            </v-card-title>
            <v-card-text>
                <v-form
                        ref="loginForm"
                        v-model="valid"
                        lazy-validation
                >
                    <v-text-field
                            label="Date"
                            required
                            outlined
                            type="date"
                            v-model="payment.date"
                    ></v-text-field>
                    <v-text-field
                            label="Title"
                            requiered
                            outlined
                            type="text"
                            v-model="payment.title"
                            :rules="titleRules"
                    ></v-text-field>
                    <v-text-field
                            label="Sum"
                            requiered
                            outlined
                            type="text"
                            v-model="payment.sum"
                            :rules="sumRules"
                    ></v-text-field>
                    <v-text-field
                            label="tax %"
                            requiered
                            outlined
                            type="text"
                            v-model="payment.tax"
                            :rules="sumRules"
                    ></v-text-field>
                    <v-switch
                            v-model="payment.paymentType"
                            label="Income/Expenses"
                            append-icon="mdi-plus"
                            prepend-icon="mdi-minus"
                    ></v-switch>
                    <v-btn
                            :disabled="!valid"
                            @click="onSubmit">Save</v-btn>
                </v-form>
            </v-card-text>
        </v-card>
    </v-dialog>
</template>
<script>

    export default {
        props: ['payment'],
        data() {
            return {
                add: false,
                date: this.nowDate(),
                title: '',
                titleRules: [
                    v => !!v || 'is required'
                ],
                sum: 1,
                tax: 0,
                sumRules: [
                    v => !!v || 'is required'
                ],
                paymentType: true,
                valid: false,
                dialog: false
            }
        },
        methods: {
            onSubmit() {
                if(this.payment.title.trim()){
                    this.$emit('edit-payment', this.payment)
                    this.dialog = false
                    this.$emit('edit-dialog', this.dialog)
                }
            },
            nowDate() {
                var today = new Date();
                var dd = today.getDate();
                var mm = today.getMonth()+1;
                var yyyy = today.getFullYear();
                if(dd<10) {
                    dd='0'+dd;
                }
                if(mm<10) {
                    mm='0'+mm;
                }
                return yyyy+'-'+mm+'-'+dd;
            }
        }
    }
</script>