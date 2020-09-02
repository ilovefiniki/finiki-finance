<template>
        <v-dialog
                v-model="addDialog"
                width="500"
        >
            <template v-slot:activator="{ on, attrs }">
                <v-btn
                       dark
                       fab
                       bottom
                       fixed
                       right
                       color="pink"
                       v-bind="attrs"
                       v-on="on"
                >
                    <v-icon>mdi-plus</v-icon>
                </v-btn>
            </template>

            <v-card class="pa-6">
                <div class="text-right">
                    <v-btn icon @click="addDialog = false">
                        <v-icon>mdi-close</v-icon>
                    </v-btn>
                </div>
                <v-card-title>
                    Add Payment
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
                                v-model="date"
                        ></v-text-field>
                        <v-text-field
                                label="Title"
                                requiered
                                outlined
                                type="text"
                                v-model="title"
                                :rules="titleRules"
                        ></v-text-field>
                        <v-select
                                v-model="currencyName"
                                :items="currencyNames"
                                label="Currency"
                        ></v-select>
                        <v-text-field
                                label="Sum"
                                requiered
                                outlined
                                type="text"
                                v-model="sum"
                                :rules="sumRules"
                        ></v-text-field>
                        <v-switch
                                v-model="paymentType"
                                label="Income/Expenses"
                                append-icon="mdi-plus"
                                prepend-icon="mdi-minus"
                        ></v-switch>
                        <v-btn
                                :disabled="!valid"
                                @click="onSubmit">Add</v-btn>
                    </v-form>
                </v-card-text>
            </v-card>
        </v-dialog>
</template>
<script>

    export default {
        props: ['currency'],
        data() {
            return {
                add: false,
                date: this.nowDate(),
                title: '',
                titleRules: [
                    v => !!v || 'is required'
                ],
                sum: 1,
                sumRules: [
                    v => !!v || 'is required'
                ],
                paymentType: true,
                addDialog: false,
                valid: false,
                currencyName: 'USD',
                currencyNames: ['USD', 'BYN'],
            }
        },
        methods: {
            onSubmit() {
                if(this.title.trim()){
                    if(this.currencyName==='BYN') {
                        this.sum = (this.sum/this.currency.buyRate).toFixed(0)
                    }
                    const payment = {
                        title: this.title,
                        date: this.date,
                        sum: this.sum,
                        currency: 'USD',
                        id: Date.now(),
                        paymentType: this.paymentType
                    }
                    this.$emit('add-payment', payment)
                    this.title = ''
                    this.addDialog = false
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