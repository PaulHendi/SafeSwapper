<template lang="html">
  <div class="col-12 q-ml-auto col-md-5">
    <q-card class="app-card">
      <q-card-section class ="card-section__image">

      <img
      :src="require('src/assets/illustration.jpg')"
      class="q-mr-sm"
      style="height: 350px">
      </q-card-section>


      <q-card-section class="q-pa-lg" v-if="BuyerText">
        <div class="row justify-between">
          <div class="column">
            <h3 class="q-my-none text-bold">{{SellerText}}</h3>
          </div>
        </div>
        <div class="row items-end">

          <q-input
            v-model="TxIDFromSeller"
            class="q-mx-auto q-gutter-md-md col-12 col-md-6 q-mb-md q-mb-md-none"
            placeholder="Tx ID"
            type="hex"
            outlined
            square
            dense
            bg-color="white"
            />

          <q-btn
          label="send GNG"
          class="text-bold self-start col-md-6 q-md-mt-auto q-my-mb-none q-md-ml-auto q-mx-auto"
          outline
          size="1rem"
          padding="sm"
          color="primary"
          @click="sendGNG"
          />

        </div>
      </q-card-section>
    </q-card>
  </div>
</template>

<script>
import { isNil, equals } from 'rambda'
import { LocalStorage } from 'quasar'
//import { Address} from "@elrondnetwork/erdjs";

import axios from 'axios'
export default {
  data () {
    return {
      BuyerText: "Input desired GNG and necessary EGLD",
      SellerText: "Input transaction ID",
      transactionData_buyer_lock: null,
      transactionData_buyer_unlock: 'unlockEGLD',
      transactionData_seller: 'sendGNG',
      DesiredGNG: 0,
      NecessaryEGLD: 0,
      TxID: null,
      TxIDFromSeller: null
    }
  },
  methods: {
    deleteFromLocalStorage (key) {
      return LocalStorage.remove(key)
    },
    async getTxID () {
    const { data } = await axios.get(`${this.elrondGatewayUrl}/transaction/${this.txHash}?withResults=true`)
    //
    return data.data.transaction.smartContractResults[0].data
       //  try {
//.transaction.nonce//.smartContractResults.data
  //    } catch (e) {
  //     throw new Error(`error: ${e.message}`)
  //    }
  //    return window.history.replaceState(null, document.title, this.baseUrl)
    },
    positiveNotify () {
      const message = 'Your transaction has been successfully completed. <br> Many thanks from the bottom of our heart ❤️'
      return this.displayNotify('positive', message, window.history.replaceState(null, document.title, this.baseUrl))
    },

    warningNotify () {
      const message = 'The field cannot be empty or equal to 0, please enter a valid amount.'
      return this.displayNotify('warning', message)
    },

    negativeNotify () {
      const message = 'We\'re sorry seems like the transaction has failed, please try again...'
      return this.displayNotify('negative', message, window.history.replaceState(null, document.title, this.baseUrl))
    },

    displayNotify (type, message, callback = () => {}) {
      this.$q.notify({
        type,
        message,
        timeout: 6000,
        html: true
      })
      return callback()
    },
    //async getAmountRaised () {
    //  try {
    //    const { data } = await axios.get(`${this.elrondGatewayUrl}/address/${this.smartContractAddress}`)
    //    return data.data.account.balance / Math.pow(10, 18)
    //  } catch (e) {
    //    throw new Error(`error: ${e.message}`)
    //  }
    //},

    getRouteParams () {
      const params = this.$router.currentRoute.value.query
      if (equals(params, {})) {
        return
      } else {
        if (LocalStorage.has('txHash')) {
          return this.deleteFromLocalStorage('txHash')
        } else {
          if (params.status) {
            this.txHash = params.txHash
            LocalStorage.set('txHash', params.txHash)
            return !!params.status && equals(params.status, 'success') ? this.positiveNotify() : this.negativeNotify()
          }
        }
      }
    },
    sendGNG ()  {
     // Note that 100 GNG is hard coded, need to query 
     this.transactionData_seller = 'ESDTTransfer@474e472d386437653035@056bc75e2d63100000@73656e64474e47@' + this.TxIDFromSeller
     const url = `${this.elrondNetworkUrl}receiver=${this.smartContractAddress}&value=0&gasLimit=${this.transactionGasLimit}&data=${this.transactionData_seller}&callbackUrl=${this.baseUrl}`
     location.assign(url)


  },

    lockEGLD ()  {
     const { data } =  axios.get(`${this.elrondGatewayUrl}/transaction/${this.txHash}?withResults=true`)
     console.log(data)
      if (equals(this.DesiredGNG, 0)) { return this.warningNotify() }
      if (equals(this.NecessaryEGLD, 0)) { return this.warningNotify() }
      if (this.NecessaryEGLD >= 0.0001 & this.DesiredGNG >= 0.0001) {
        this.DesiredGNG_Decimals = this.DesiredGNG * Math.pow(10, 18)
        this.NecessaryEGLD_Decimals = this.NecessaryEGLD * Math.pow(10, 18)
        // Need to add a 0 after @
        this.transactionData_buyer_lock = 'lockEGLD@0' + this.DesiredGNG_Decimals.toString(16)
        const url = `${this.elrondNetworkUrl}receiver=${this.smartContractAddress}&value=${this.NecessaryEGLD_Decimals}&gasLimit=${this.transactionGasLimit}&data=${this.transactionData_buyer_lock}&callbackUrl=${this.baseUrl}`
        location.assign(url)
        //const url_fetch = https://devnet-gateway.elrond.com/transaction/bc0f3e9954a3a59d058183fffbf1f43e1ace34837038ff6dad78d3849c56d89f?withResults=true
      }
    }
  },
  computed: {
    baseUrl () { return 'http://localhost:8080/' },
    collectionID () { return 'WARMY-111ba7' },
    elrondNetworkUrl () { return 'https://devnet-wallet.elrond.com/hook/transaction?' },
    elrondGatewayUrl () { return 'https://devnet-gateway.elrond.com'},
    pictureUrl () { return 'https://ipfs.io/ipfs/QmaTjALTzsFgDFUs9Wy8PhFVUZ7ARbm2pPVEQkQmHiLJDm' },
    smartContractAddress () { return 'erd1qqqqqqqqqqqqqpgqawd4a9zxqmku6nw4qjmkqzj4y69vag0xkewq5wgzjc' },
    transactionGasLimit () { return 250000000 }
  },
  async created () {
  await this.getRouteParams()
  this.TxID = await this.getTxID()
  this.deleteFromLocalStorage('txHash')

  this.TxID = this.TxID.split("@")[2]
  console.log(this.TxID)

  //  try {

  //    if (!isNil(this.TxID)) {
//      console.log(this.TxID)
  //    }

  //  } catch (e) {
//      throw new Error('error detected: ', e.message)
//    }

  },

  beforeRouteLeave(to, from) {
    this.deleteFromLocalStorage('txHash')
  }
}
</script>
