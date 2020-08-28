<template>
  <v-container>
    <v-row class="text-center">
      <v-col cols="6">
        <v-img
          :src="require('../assets/book.png')"
          class="my-3"
          contain
          height="400"
        />
      </v-col>

      <v-col cols="6">
        <div class="payment_wrapper">
          <div class="payment__layout">
            <h1>Buy Book</h1>
            <h3>Amount - $10</h3>
          </div> 

          <div class="payment__layout payment-form">
            <v-text-field label="Full Name" type="text" :rules="[rules.required]" v-model="name" outlined dense></v-text-field>
            <v-text-field label="Email" type="email" :rules="[rules.required, rules.email]" v-model="email" outlined dense></v-text-field>
            <stripe-elements
              ref="elementsRef"
              :pk="publishableKey"
              :amount="amount"
              locale="en"
              @token="tokenCreated"
              @loading="loading = $event"
            >
            </stripe-elements>
          </div>
          <button @click="submit">Pay ${{amount / 100}}</button>
        </div>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
  import { StripeElements } from 'vue-stripe-checkout';
  import axios from 'axios';

  export default {
    components: {
      StripeElements
    },
    data: () => ({
      loading: false,
      amount: 1000,
      // later move publick key to env file for production
      publishableKey: "pk_test_51HJgjtIWWRRN4vLSs1jwkvGgLcDW7QcWA64dBoAwhXN0hzkEP50jObA3ZcDYOUUwXz5BbvnZoBvo1tcYARpcqmdD00SFArXJzs", 
      token: null,
      description: "This is a test payment",
      charge: null,
      email: "sacin@test.com",
      name: "sachin g",
      address: {"city":"mumbai","country":"india","line1":"unr","line2":"thane","postal_code":"421005","state":"maharashtra"},
      rules: {
        required: value => !!value || 'Required.',
        email: value => {
          const pattern = /^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/
          return pattern.test(value) || 'Invalid e-mail.'
        },
      },
    }),
    methods: {
      submit () {
        this.$refs.elementsRef.submit();
      },
      tokenCreated (token) {
        console.warn(token);
        this.token = token;
        this.charge = {
          stripeToken: token.id,
          amount: this.amount,
          description: this.description,
          email: this.email,
          name: this.name,
          address: this.address
        }
        this.sendTokenToServer(this.charge);
      },
      sendTokenToServer (charge) {
        // later add "http://127.0.0.1:8000/" in baseUrl for production
        axios
        .post("http://127.0.0.1:8000/payment", charge, {headers: {'Access-Control-Allow-Origin': '*'}})
        .then(function(response) {
          console.log(response)
          alert('Payment successful!!')
        })
        .catch(function(error) {
          console.log(error);
          alert('Payment failed!!')
        });
      }
    }
  }
</script>

<style scoped>
/* BEM standard */
.payment_wrapper{
  margin: 0;
}
.payment__layout{
  display: flex;
  justify-content: space-between;
  padding: 0.5em 1em;
  align-items: center;
  background-color: #e0e0e0;
  border-radius: 5px;
  margin: 0 0 2em;
}
.payment-form{
  padding: 0.9em 0.5em;
  background-color: #e0e0e0;
  border-radius: 5px;
  margin: 0 0 2em;
  display: block;
}
.user-form{
  display: block;
  padding: 1em 0.5em;
}
button{
  background-color: rgb(61, 66, 78);
  color: rgb(255, 255, 255);
  padding: 0.5em 1.7em;
  font-size: 1.2em;
  border-radius: 5px;
  font-weight: 600;
  float: left;
}
</style>