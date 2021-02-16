<template>
    <div class="text-center">
        Total:
        <v-chip
                class="ma-2 font-weight-bold"
                color="indigo"
                text-color="white"
        >
            <v-avatar left>
                <v-icon>mdi-plus</v-icon>
            </v-avatar>
            {{ totalIncome }}$ ({{ (totalIncome*currency.buyRate).toFixed(0) }} BYN)
        </v-chip>
        <v-chip
                class="ma-2"
                color="orange"
                text-color="white"
        >
            <v-icon left>mdi-minus</v-icon>
            {{ totalExpenses }}$ ({{ (totalExpenses*currency.buyRate).toFixed(0) }} BYN)
        </v-chip>

        <v-chip
                class="ma-2"
                color="primary"
                text-color="white"
        >
            <v-avatar left>
                <v-icon>mdi-checkbox-marked-circle</v-icon>
            </v-avatar>
            {{ totalIncome - totalExpenses }}$ ({{ ((totalIncome - totalExpenses)*currency.buyRate).toFixed(0) }} BYN)
        </v-chip>
        <v-chip
                class="ma-2 font-weight-light"
                color="indigo"
                outlined
                v-if="fullYear"
        >
            <v-avatar left>
                <v-icon>mdi-sine-wave</v-icon>
            </v-avatar>
            {{ totalAverage }}$ ({{ ((totalAverage)*currency.buyRate).toFixed(0) }} BYN)
        </v-chip>
    </div>
</template>
<script>

    export default {
        currentUser: {},
        props: ['payments', 'currency', 'date', 'fullYear'],
        data() {
            return {
            }
        },
        mounted() {
        },
        computed: {
            totalIncome() {
                let total = 0
                this.payments.forEach(val => {
                    if(val.paymentType) {
                        total += Number(val.sum)
                    }
                });
                return total
            },
            totalExpenses() {
                let total = 0
                this.payments.forEach(val => {
                    if(!val.paymentType) {
                        total += Number(val.sum)
                    }
                });
                return total
            },
            totalAverage() {
                let total = 0
                this.payments.forEach(val => {
                    if(val.paymentType) {
                        total += Number(val.sum)
                    }
                });
                const currentMonth = new Date(Date.parse(this.date)).getMonth()+1
                return (total/currentMonth).toFixed(0)
            }
        },
        methods: {

        }
    }
</script>