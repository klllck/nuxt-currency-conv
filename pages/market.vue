<i18n lang="yaml">
en:
  title: "Latest Exchange Rates"
  baseCurrencyPlaceholder: "Base currency"
  searchCurrencyPlaceholder: "Search currency..."
  currency: "Сurrency"
  exchangeRate: "Exchange Rate (relative to the base currency)"
  notSupported: "is not supported yet"
  supportedCurrencies: "Currencies that currently available on this web resourse:"
ru:
  title: "Курсы валют на данный момент"
  baseCurrencyPlaceholder: "Базовая валюта"
  searchCurrencyPlaceholder: "Искать валюту..."
  currency: "Валюта"
  exchangeRate: "Обменный курс (относительно базовой валюты)"
  notSupported: "на данный момент не поддерживается"
  supportedCurrencies: "Валюты, которые поддерживаются на нашем цифровом ресурсе:"
</i18n>

<template>
  <v-container>
    <h1>{{ $t("title") }}</h1>
    <br />
    <v-select
      :items="currenciesNames"
      v-model="baseCurrency"
      @change="loadDataFromAPI()"
      :label="$t('baseCurrencyPlaceholder')"
      outlined
    ></v-select>
    <div v-if="!isBaseCurrencySupported">
      <h3>{{ baseCurrency }} {{ $t("notSupported") }}</h3>
      <br />
      <h2>{{ $t("supportedCurrencies") }}</h2>
      <v-card v-for="item in validCurrencies.split('\n')" tile>
        <v-list-item>
          <v-list-item-content>
            <v-list-item-title>{{ item }}</v-list-item-title>
          </v-list-item-content>
        </v-list-item>
      </v-card>
    </div>
    <v-data-table
      v-else
      :headers="headers"
      :items="filteredCurrencies"
      :items-per-page="8"
      class="elevation-1"
      :search="searchQuery"
    >
      <template v-slot:top>
        <v-text-field
          v-model="searchQuery"
          :label="$t('searchCurrencyPlaceholder')"
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
        { text: this.$t("currency"), value: "name" },
        { text: this.$t("exchangeRate"), value: "rate" },
      ],
      currencies: [],
      currenciesNames: [],
      validCurrencies:
       `Australian Dollar (AUD)
        Brazilian Real (BRL)
        British Pound Sterline (GBP)
        Bulgarian Lev (BGN)
        Canadian Dollar (CAD)
        Chinese Yuan Renminbi (CNY)
        Croatian Kuna (HRK)
        Czech Koruna (CZK)
        Danish Krone (DKK)
        Euro (EUR)
        Hong Kong Dollar (HKD)
        Hungarian Forint (HUF)
        Icelandic Krona (ISK)
        Indonesian Rupiah (IDR)
        Indian Rupee (INR)
        Israeli Shekel (ILS)
        Japanese Yen (JPY)
        Malaysian Ringgit (MYR)
        Mexican Peso (MXN)
        New Zealand Dollar (NZD)
        Norwegian Krone (NOK)
        Philippine Peso (PHP)
        Polish Zloty (PLN)
        Romanian Leu (RON)
        Russian Rouble (RUB)
        Singapore Dollar (SGD)
        South African Rand (ZAR)
        South Korean Won (KRW)
        Swedish Krona (SEK)
        Swiss Franc (CHF)
        Thai Baht (THB)
        Turkish Lira (TRY)
        US Dollar (USD)`,
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
