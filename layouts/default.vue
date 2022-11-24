<template>
  <v-app light>
    <v-navigation-drawer
      v-model="drawer"
      :mini-variant="miniVariant"
      :clipped="clipped"
      fixed
      app
    >
      <v-list>
        <v-list-item v-for="(item, i) in items" :key="i" :to="item.to" router exact>
          <v-list-item-action>
            <v-icon>{{ item.icon }}</v-icon>
          </v-list-item-action>
          <v-list-item-content>
            <v-list-item-title :to="localePath(item.to)">{{
              $t(item.title)
            }}</v-list-item-title>
          </v-list-item-content>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>
    <v-app-bar :clipped-left="clipped" app>
      <v-app-bar-nav-icon @click.stop="drawer = !drawer" />
      <v-btn icon @click.stop="miniVariant = !miniVariant">
        <v-icon>mdi-{{ `chevron-${miniVariant ? "right" : "left"}` }}</v-icon>
      </v-btn>
      <v-btn icon @click.stop="clipped = !clipped">
        <v-icon>mdi-application</v-icon>
      </v-btn>
      <v-toolbar-title>{{ title }}</v-toolbar-title>
      <v-spacer />
      <v-menu offset-y>
        <template v-slot:activator="{ on, attrs }">
          <v-btn icon>
            <v-icon v-bind="attrs" v-on="on">mdi-translate </v-icon>
          </v-btn>
        </template>
        <v-list>
          <v-list-item>
            <nuxt-link
              :to="switchLocalePath('en')"
              style="text-decoration: none; color: inherit"
              >English</nuxt-link
            >
          </v-list-item>
          <v-list-item>
            <nuxt-link
              :to="switchLocalePath('ru')"
              style="text-decoration: none; color: inherit"
              >Русский</nuxt-link
            >
          </v-list-item>
        </v-list>
      </v-menu>
    </v-app-bar>
    <v-main>
      <v-container>
        <Nuxt />
      </v-container>
    </v-main>
    <v-footer :absolute="fixed" app class="justify-center">
      <span>&copy; {{ new Date().getFullYear() }} Currency Converter Lite</span>
    </v-footer>
  </v-app>
</template>

<i18n lang="yaml">
en:
  converter: "Converter"
  market: "Market"
ru:
  converter: "Конвертер"
  market: "Курсы валют"
</i18n>

<script>
export default {
  name: "DefaultLayout",
  data() {
    return {
      drawer: true,
      clipped: true,
      fixed: false,
      items: [
        {
          icon: "mdi-cash-multiple",
          title: "converter",
          to: "converter",
        },
        {
          icon: "mdi-bank",
          title: "market",
          to: "market",
        },
      ],
      miniVariant: false,
      title: "Currency Converter Lite",
    };
  },
};
</script>
