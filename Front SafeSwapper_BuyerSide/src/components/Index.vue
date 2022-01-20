<template>
  <q-layout view="hHh lpR fFf">
    <Navbar/>
    <q-page-container>
      <q-page>
        <div class="container">
          <Header/>
        </div>
      </q-page>
    </q-page-container>
    <Footer/>
  </q-layout>
</template>
<script>
import Navbar from '@components/Navbar.vue'
import Header from '@components/Header.vue'
import Footer from '@components/Footer.vue'
export default {
  components: {
    Navbar,
    Header,
    Footer
  }
}
</script>
