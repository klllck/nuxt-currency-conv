<template>
  <v-container>
    <h1>Latest Exchange Rates</h1>
    <br />
    <v-select
      :items="currenciesNames"
      v-model="baseCurrency"
      @change="loadDataFromAPI()"
      label="Base Currency"
      outlined
    ></v-select>
    <h3 v-if="!isBaseCurrencySupported">{{ baseCurrency }} is not supported yet</h3>
    <v-data-table
      :headers="headers"
      :items="filteredCurrencies"
      :items-per-page="10"
      class="elevation-1"
      :search="searchQuery"
    >
      <template v-slot:top>
        <v-text-field
          v-model="searchQuery"
          label="Search currency..."
          class="mx-4"
        ></v-text-field>
      </template>
    </v-data-table>
  </v-container>
</template>

<script>
import { exchangeRates } from "exchange-rates-api";

export default {
  name: "MarketPage",
  data() {
    return {
      headers: [
        { text: "Currency", value: "name" },
        { text: "Exchange Rate", value: "rate" },
      ],
      currencies: [],
      currenciesNames: [],
      baseCurrency: "USD",
      searchQuery: "",
      isBaseCurrencySupported: true,
    };
  },
  async fetch() {
    this.loadDataFromAPI();
  },
  computed: {
    filteredCurrencies() {
      return this.currencies.filter((currency) =>
        currency.name.toLowerCase().includes(this.searchQuery.toLowerCase())
      );
    },
  },
  methods: {
    async loadDataFromAPI() {
      try {
        const response = await exchangeRates()
          .setApiBaseUrl("https://api.exchangerate.host")
          .latest()
          .base(this.baseCurrency)
          .fetch();

        this.currenciesNames = Object.keys(response);

        const validCurrencies = Object.entries(response).map(([name, rate]) => ({
          name,
          rate,
        }));

        this.currencies = validCurrencies;
        this.isBaseCurrencySupported = true;
      } catch (error) {
        console.warn(error);
        this.isBaseCurrencySupported = false;
      }
    },
  },
};
</script>
