<template>

  <v-container fluid>
    <header-tab :wallet=Wallet @loadContract="onLoadAccounts"></header-tab>
    <new-account-card v-if="Wallet != null" @click="showCreateForm"></new-account-card>
    <v-row v-for="(item, index) in Accounts" :key="index" class="mt-6" no-gutters>
      <account-card @deposit="showDeposit" @withdraw="showWithdraw" @transfer="showTransfer" :account=item>
      </account-card>
    </v-row>

    <v-row justify="center">
      <v-dialog v-model="dialog" persistent max-width="1000px" @click:outside="closeDialog">
        <create-account-form @createAccount="createAccount"></create-account-form>
      </v-dialog>
    </v-row>

    <v-row justify="center">
      <v-dialog v-model="deposit" persistent max-width="1000px" @click:outside="closeDialog">
        <deposit-form @depositClicked="depositClicked"></deposit-form>
      </v-dialog>
    </v-row>

    <v-row justify="center">
      <v-dialog v-model="withdraw" persistent max-width="1000px" @click:outside="closeDialog">
        <withdraw-form @withdrawClicked="withdrawClicked"></withdraw-form>
      </v-dialog>
    </v-row>

    <v-row justify="center">
      <v-dialog v-model="transfer" persistent max-width="1000px" @click:outside="closeDialog">
        <transfer-form @transferClicked="transferClicked"></transfer-form>
      </v-dialog>
    </v-row>

  </v-container>

</template>

<script>
import HeaderTab from './components/HeaderTab.vue';
import NewAccountCard from './components/NewAccountCard.vue';
import AccountCard from './components/AccountCard.vue';
import CreateAccountForm from './components/CreateAccountForm.vue';
import DepositForm from './components/DepositForm.vue';
import WithdrawForm from './components/WithdrawForm.vue';
import TransferForm from './components/TransferForm.vue';

import Web3 from 'web3/dist/web3.min.js';
import configuration from '../build/contracts/Accounts.json';

export default {
  name: 'App',
  components: {
    // AccountItem
    HeaderTab,
    CreateAccountForm,
    NewAccountCard,
    AccountCard,
    DepositForm,
    WithdrawForm,
    TransferForm
  },
  data() {
    return {
      Accounts: [],
      Contract: null,
      Wallet: null,
      dialog: false,
      deposit: false,
      withdraw: false,
      transfer: false,
      selectedAccount: null
    };
  },
  methods: {
    async onLoadAccounts() {
      if (typeof window.ethereum !== 'undefined') {
        if (window.ethereum.isMetaMask) {
          const web3 = new Web3(window.ethereum);
          const accounts = await window.ethereum.request({ method: 'eth_requestAccounts' });
          const account = accounts[0];
          // const web3 = new Web3(new Web3.providers.HttpProvider('http://localhost:7545'))
          // const accounts = await web3.eth.getAccounts()

          this.Wallet = account;

          const CONTRACT_ADDRESS = configuration.networks['5777'].address;
          const CONTRACT_ABI = configuration.abi;
          const contract = new web3.eth.Contract(CONTRACT_ABI, CONTRACT_ADDRESS);

          this.Contract = contract;

          const tempAccounts = await this.Contract.methods.accountOf(this.Wallet).call()
          this.Accounts = tempAccounts.filter(function (el) {
            return el.accountname != "";
          });
        }
      }
    },
    showCreateForm() {
      this.dialog = !this.dialog;
    },
    async createAccount(value) {
      this.dialog = false;
      await this.Contract.methods.addAccount(value).send({ from: this.Wallet, gas: 3000000 });

      this.Accounts = await this.Contract.methods.accountOf(this.Wallet).call();
    },
    showDeposit(value) {
      this.deposit = !this.deposit
      this.selectedAccount = value
    },
    showWithdraw(value) {
      this.withdraw = !this.withdraw
      this.selectedAccount = value
    },
    showTransfer(value) {
      this.transfer = !this.transfer
      this.selectedAccount = value
    },
    closeDialog() {
      this.deposit = false
      this.withdraw = false
      this.transfer = false
    },
    async depositClicked(value) {
      this.deposit = false
      await this.Contract.methods.deposit(this.selectedAccount.accountname).send({ from: this.Wallet, gas: 3000000, value: Web3.utils.toWei(value, 'ether') });

      this.Accounts = await this.Contract.methods.accountOf(this.Wallet).call();
    },
    async withdrawClicked(value) {
      this.withdraw = false
      await this.Contract.methods.withdraw(this.selectedAccount.accountname, Web3.utils.toWei(value, 'ether')).send({ from: this.Wallet, gas: 3000000 });

      this.Accounts = await this.Contract.methods.accountOf(this.Wallet).call();
    },
    async transferClicked(value) {
      this.transfer = false
      console.log(value);
      await this.Contract.methods.transfer(this.selectedAccount.accountname, value.name, Web3.utils.toWei(value.amount, 'ether')).send({ from: this.Wallet, gas: 3000000 });

      this.Accounts = await this.Contract.methods.accountOf(this.Wallet).call();
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.card {
  border-style: dashed;
  border-color: rgb(200, 200, 200);
  border-width: 4px;
  border-radius: 10px;
}
</style>
