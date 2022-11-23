<i18n lang="yaml">
en:
  title: "Currency Converter"
  label: "Enter your query in the textbox below:"
  placeholder: "For example: 15 usd in rub"
  convertBtn: "Convert"
  clearBtn: "Clear"
  required: "Query is required!"
  invalidQuery: "The entered query must be valid! Please, check the example and type it correct."
  invalidCurrency: "is not supported yet"
ru:
  title: "Конвертер валют"
  label: "Введите запрос в текстовое поле ниже:"
  placeholder: "Например: 15 usd in rub"
  convertBtn: "Конвертировать"
  clearBtn: "Очистить"
  required: "Необходимо ввести запрос!"
  invalidQuery: "Запрос должен быть валидным! Пожалуйста, рассмотрите пример, а затем введите валидный запрос."
  invalidCurrency: "не поддерживается на данный момент"
</i18n>

<template>
  <form @submit.prevent="submit">
    <v-container>
      <h1>{{ $t("title") }}</h1>
      <br />
      <v-label>{{ $t("label") }}</v-label>
      <v-text-field
        v-model="query"
        :label="$t('placeholder')"
        required
        focus
      ></v-text-field>
      <v-btn class="mr-4" @click="submit"> {{ $t("convertBtn") }} </v-btn>
      <v-btn @click="clear"> {{ $t("clearBtn") }} </v-btn>
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
          this.result = this.$t("required");
          return;
        }
        const [amount, from, _, to] = this.query.split(" ");

        const response = await exchangeRates()
          .setApiBaseUrl("https://api.exchangerate.host")
          .latest()
          .base(from)
          .symbols(to)
          .fetch()
          .then((rate) => rate * amount);

        if (isNaN(response)) {
          this.result = this.$t("invalidQuery");
          return;
        }
        const resultAmount = from !== to ? response.toFixed(4) : response;
        this.result = `${resultAmount} ${to.toUpperCase()}`;
      } catch (error) {
        console.warn(error);

        if (error instanceof TypeError) {
          this.result = this.$t("invalidQuery");
          return;
        }

        const invalidCurrency = error.toString().split(" ").slice(1, 2).join();
        if (invalidCurrency.length <= 0) {
          this.result = this.$t("invalidQuery");
          return;
        }

        this.result = invalidCurrency + " " + this.$t("invalidCurrency");
      }
    },
    clear() {
      this.query = "";
      this.result = "";
    },
  },
};
</script>
