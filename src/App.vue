<template>
  <v-app id="inspire" dark>
    <v-navigation-drawer
      v-model="drawer"
      app
      :clipped="$vuetify.breakpoint.lgAndUp"
    >
      <v-list dense>
        <template>
          <v-list-item :to="{ name: 'home' }">
            <v-list-item-action>
              <v-icon> mdi-account </v-icon>
            </v-list-item-action>
            <v-list-item-title>
              Users
            </v-list-item-title>
          </v-list-item>
        </template>

        <template>
          <v-list-item>
            <v-list-item-action>
              <v-icon> mdi-settings </v-icon>
            </v-list-item-action>
            <v-list-item-title>
              Settings
            </v-list-item-title>
          </v-list-item>
        </template>
      </v-list>
    </v-navigation-drawer>

    <v-app-bar
      app
      clipped-left
    >
      <v-app-bar-nav-icon @click.stop="drawer = !drawer"></v-app-bar-nav-icon>
      <v-toolbar-title>Painel de Controle</v-toolbar-title>
    </v-app-bar>

    <v-content>
      <v-container
        fluid
        fill-height
      >
        <v-slide-y-transition mode="out-in">
          <router-view />
        </v-slide-y-transition>
      </v-container>
    </v-content>

    <v-footer app>
      <span>Nested Development &copy; 2019</span>
    </v-footer>
  </v-app>
</template>

<script>
  export default {
    data: () => ({
      drawer: null
    }),
  }
</script>