<template>
  <v-app>
    <v-main>
      <PaymentComponent/>
    </v-main>
  </v-app>
</template>

<script>
import PaymentComponent from './components/PaymentComponent';

export default {
  name: 'App',

  components: {
    PaymentComponent,
  },

  data: () => ({
    //
  }),
};
</script>
