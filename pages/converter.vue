<template>
  <form @submit.prevent="submit">
    <v-container>
      <h1>Currency Converter</h1>
      <br />
      <v-label>Enter your query in the textbox below:</v-label>
      <v-text-field
        v-model="query"
        label="For example: 15 usd in rub"
        required
        focus
      ></v-text-field>
      <v-btn class="mr-4" @click="submit"> convert </v-btn>
      <v-btn @click="clear"> clear </v-btn>
    </v-container>
    <br />
    <h2 class="px-4">{{ result }}</h2>
  </form>
</template>

<script>
import { exchangeRates } from "exchange-rates-api";

export default {
  name: "ConverterPage",
  data() {
    return {
      query: "",
      result: "",
    };
  },
  methods: {
    async submit() {
      try {
        if (this.query.length <= 0) {
          this.result = "Query is required!";
          return;
        }
        const [amount, from, _, to] = this.query.split(" ");

        const res = await exchangeRates()
          .setApiBaseUrl("https://api.exchangerate.host")
          .latest()
          .base(from)
          .symbols(to)
          .fetch()
          .then((rate) => rate * amount);

        this.result = `${res} ${to.toUpperCase()}`;
      } catch (error) {
        console.warn(error);

        if (error instanceof TypeError) {
          this.result =
            "The enter query must be valid! Please, check the example and type it correct.";
          return;
        }
        this.result = error.toString().split(':')[1];
      }
    },
    clear() {
      this.query = "";
      this.result = "";
    },
  },
};
</script>
