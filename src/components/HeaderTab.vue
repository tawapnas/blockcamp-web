<template>
    <v-row class="mb-20" no-gutters>
        <v-col>
            <div class="text-h4">
                🚀10XBank
            </div>
        </v-col>

        <v-spacer></v-spacer>

        <v-col>
            <v-btn v-if="wallet == null" @click="greet()" class="pa-2 mr-4" depressed color="primary">
                Connect Wallet
            </v-btn>
            <v-btn v-else class="pa-2 mr-4" depressed color="primary">
                {{ wallet }}
            </v-btn>
        </v-col>
    </v-row>
</template>

<script>
// import HDWalletProvider from '@truffle/hdwallet-provider';

export default {
    props: {
        wallet: String
    },
    methods: {
        async greet() {
            this.$emit('loadContract', null);
        }
    }
}
</script>