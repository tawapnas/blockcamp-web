<template>
    <v-card>
        <v-card-title>
            <span class="text-h5">Withdraw</span>
        </v-card-title>
        <v-card-text>
            <v-container>
                <v-row>
                    <v-col cols="11">
                        <v-text-field v-model="amount" label="Amount*" required></v-text-field>
                    </v-col>
                    <v-col cols="1">
                        ETH
                    </v-col>
                </v-row>
            </v-container>
        </v-card-text>
        <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="blue darken-1" text @click="create">
                Withdraw
            </v-btn>
        </v-card-actions>
    </v-card>
</template>

<script>
export default {
    data() {
        return {
            amount: ""
        }
    },
    methods: {
        create() {
            this.$emit('withdrawClicked', this.amount);
        }
    }
}
</script>