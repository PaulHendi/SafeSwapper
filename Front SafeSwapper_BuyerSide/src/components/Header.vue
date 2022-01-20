<template lang="html">
  <div id="section-header" class="q-mt-xl">
    <div class="row q-md-gutter-xl">
      <div class="col-md-5 column">
        <h1 class="text-h5 q-mb-none section-header text-underlined">Safe Swapper</h1>
        <h2 class="text-bold q-mt-none q-mt-xl q-mb-md">How does it work? </h2>
          <div class="section-header__text">
            <p>
                To make it simple, there is a <span class="text-bold">Buyer</span> and a <span class="text-bold">Seller</span>.
                The Buyer buys GNG with EGLD and the Seller sells GNG to get EGLD.
            </p>
            <p>
                The swap is always <span class="text-bold">initiated by the Buyer</span>, he needs to input the desired amount of GNG
                and lock the amount of EGLD agreed upon in the smart contract. Once the EGLD locked, he will receive a
                <span class="text-bold">transaction id</span> that is necessary for the Seller.
            </p>
            <p>
                The Seller waits for the Buyer to send the transaction id as it contains the necessary information. Once received,
                the Seller can input it and agree to the the amount of EGLD and send the correct amount of GNG to the smart contract.
            </p>
            <p>
                If both amounts are correct and agreed upon, the transaction occurs. If not, the Seller gets automatically his GNG back
                and the Buyer can unlock his EGLD.
            </p>
        </div>
      </div>
      <CardNft/>
    </div>
  </div>
</template>
<script>
import CardNft from './CardNft.vue'
export default {
  components: {
    CardNft
  }
}
</script>
