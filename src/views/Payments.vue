<template>
    <v-container>
        <v-card class="my-2 pa-3">
            <v-row align="center">
                <v-col class="d-flex" cols="12" sm="3">
                    <v-checkbox
                            v-model="fullYear"
                            label="From start of the year"
                            @click="getPayments"
                    ></v-checkbox>
                </v-col>
                <v-col class="d-flex" cols="12" sm="3">
                    <v-dialog
                            ref="dialog"
                            v-model="modal"
                            :return-value.sync="date"
                            persistent
                            width="290px"
                    >
                        <template v-slot:activator="{ on, attrs }">
                            <v-text-field
                                    v-model="date"
                                    label="Payments month:"
                                    prepend-icon="mdi-calendar"
                                    readonly
                                    v-bind="attrs"
                                    v-on="on"
                            ></v-text-field>
                        </template>
                        <v-date-picker v-model="date" type="month" scrollable>
                            <v-spacer></v-spacer>
                            <v-btn text color="primary" @click="modal = false">Cancel</v-btn>
                            <v-btn text color="primary" @click="$refs.dialog.save(date); getPayments()">OK</v-btn>
                        </v-date-picker>
                    </v-dialog>
                </v-col>
            </v-row>
        </v-card>

        <div class="white">
            <Loading align="center" v-if="loading" />
            <v-data-table
                    v-if="!loading"
                    :headers="headers"
                    :items="payments"
                    :items-per-page="50"
                    :footer-props="{
                                      itemsPerPageOptions: [10,20,50,100,-1]
                                    }"
                    sort-by="date"
                    class="elevation-1"
                    mobile-breakpoint="350"
            >
                <template v-slot:item.sum="{ item }">
                    <div class="font-weight-bold text-right">{{ item.sum | sumType(item.paymentType) }}</div>
                </template>
                <template v-slot:item.netto="{ item }">
                    <div class="font-weight-bold text-right">{{ item.sum | nettoSum(item.paymentType, item.tax) }}</div>
                </template>
                <template v-slot:item.action="{ item }">
                    <EditPayment
                            :payment="item"
                            @edit-payment="editPayment"
                    />
                 <!--   <v-btn icon @click="editDialogShow(item)"><v-icon>mdi-pencil</v-icon></v-btn> -->
                    <v-btn icon @click="deletePayment(item.id)"><v-icon>mdi-delete</v-icon></v-btn>
                </template>
            </v-data-table>
            <Total
                   :payments = "payments"
                   :currency = "currency"
                   :date = "date"
                   :full-year = "fullYear"
            />
        </div>
        <AddPayment
                @add-payment="addPayment"
                :currencies = "currencies"
        />
    </v-container>
</template>
<script>
    import Total from '@/components/Total'
    import AddPayment from '@/components/AddPayment'
    import EditPayment from '@/components/EditPayment'
    import Loading from '@/components/Loading'
    import * as firebase from 'firebase/app'
    import 'firebase/auth'
    import 'firebase/database';

    export default {
        data() {
            return {
                currentUser: {},
                headers: [
                    {text: 'date', value: 'date'},
                    {text: 'Brutto', value: 'sum'},
                    {text: 'Netto', value: 'netto'},
                    {text: 'Title', value: 'title'},
                    {text: 'Action', value: 'action'}
                ],
                payments: [],
                loading: true,
                date: new Date().toISOString().substr(0, 7),
                menu: false,
                modal: false,
                editItem: {},
                editDialog: false,
                currency: {},
                currencies: {},
                fullYear: false
            }
        },
        mounted() {
            this.getPayments()
            this.getCurrency()
        },
        filters: {
            sumType(sum, type) {
                //console.log(type)
                if(!type) {
                    return '-'+sum
                } else {
                    return '+'+sum
                }
            },
            nettoSum(sum, type, tax) {
                if(!type) {
                    return '-'
                } else {
                    if(!tax){
                        tax=0;
                    }
                    const result = sum*(100-tax)/100;
                    return '+'+result.toFixed(0)
                }

            }
        },
        methods: {
            async getPayments() {
                try{
                    this.currentUser = await firebase.auth().currentUser
                    if(this.currentUser) {
                        let startDate = '';
                        if(this.fullYear){
                            const startOfTheYear = new Date(Date.parse(this.date)).getFullYear()
                            startDate = startOfTheYear+'-01-01'
                        } else {
                            startDate  = this.date
                        }
                        const endDate = this.date+this.daysInMonth(this.date)
                        const dbdata = (await firebase.database().ref().child(`payments/${this.currentUser.uid}`).orderByChild("date").startAt(startDate).endAt(endDate).once('value')).val()
                        this.payments = []
                        this.payments = Object.values(dbdata)
                        this.loading = false
                    } else {
                        this.$router.push('/login')
                    }
                } catch (e) {
                    console.log(e.message)
                    this.loading = false
                }
            },
            async editPayment(payment) {
                console.log(payment)
                this.loading = true
                try {
                    this.currentUser = await firebase.auth().currentUser
                    if(this.currentUser) {
                        await firebase.database().ref(`payments/${this.currentUser.uid}/${payment.id}`).update(payment)
                        this.getPayments()
                    }
                    this.loading = false
                } catch(e) {
                    this.loading = false
                    console.log(e.message)
                }
            },
            async deletePayment(id) {
                this.loading = true
                try {
                    this.currentUser = await firebase.auth().currentUser
                    if(this.currentUser) {
                        await firebase.database().ref(`payments/${this.currentUser.uid}/${id}`).remove()
                        this.payments = this.payments.filter(payment => payment.id != id)
                    }
                    this.loading = false
                } catch(e) {
                    this.loading = false
                    console.log(e.message)
                }
            },
            async addPayment(payment) {
                this.loading = true
                try {
                    this.currentUser = await firebase.auth().currentUser
                    if(this.currentUser) {
                        await firebase.database().ref(`payments/${this.currentUser.uid}/${payment.id}`).set(payment)
                        this.getPayments()
                    }
                    this.loading = false
                } catch(e) {
                    this.loading = false
                    console.log(e.message)
                }
            },
            async getCurrency() {
                console.log('get currency')
                const axios = require('axios')
                axios
                    .get('https://developerhub.alfabank.by:8273/partner/1.0.0/public/rates')
                    .then(response => {
                        if(response.data) {
                            this.currencies = response.data.rates
                            this.currency = response.data.rates.filter( (rate) => rate.sellCode==840 && rate.buyCode == 933)
                            this.currency = this.currency[0]
                            //console.log(this.currency)
                        }
                    })
                    .catch(error => console.log(error))
            },
            daysInMonth(date) {
                date = new Date(date);
                var month = date.getMonth() + 1;
                var year = date.getFullYear();
                return new Date(year, month, 0).getDate()
            }
    },
        components: {
            AddPayment,
            EditPayment,
            Loading,
            Total
        }
    }
</script>