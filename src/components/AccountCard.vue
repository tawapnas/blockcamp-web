<template>
    <v-col>
        <v-card outlined class="ma-6 pa-10 text-h6">
            <v-row no-gutters>
                Account Name: {{ account.accountname }}
            </v-row>
            <v-row no-gutters>
                Balance: {{ balance }} ETH
            </v-row>
            <v-row class="mt-4" no-gutters>
                <v-col class="ml-4 mr-4">
                    <v-btn @click="onClickDeposit()" min-width="100%">
                        🟩 Deposit
                    </v-btn>
                </v-col>
                <v-col class="ml-4 mr-4">
                    <v-btn @click="onClickWithdraw()" min-width="100%">
                        🟥 Withdraw
                    </v-btn>
                </v-col>
                <v-col class="ml-4 mr-4">
                    <v-btn @click="onClickTransfer()" min-width="100%">
                        🟦 Transfer
                    </v-btn>
                </v-col>
            </v-row>
        </v-card>
    </v-col>
</template>

<script>
import Web3 from 'web3/dist/web3.min.js';

export default {
    props: {
        account: {
            type: Object,
            default: () => ({})
        }
    },
    data() {
        return {
            balance: Web3.utils.fromWei(this.account.balance, 'ether')
        }
    },
    methods: {
        onClickTransfer() {
            this.$emit("transfer", this.account);
        },
        onClickDeposit() {
            this.$emit("deposit", this.account);
        },
        onClickWithdraw() {
            this.$emit("withdraw", this.account);
        }
    }
}
</script>