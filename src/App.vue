

<template>
  <v-app>
    <v-app-bar title="Application bar" flat border="b">
      <TheBreadcrumbs />
    </v-app-bar>
    <v-main class="fill-height">
        <v-container class="ma-2">
          <RouterView />
      </v-container>
    </v-main>
  </v-app>
</template>
<script setup lang="ts">
import TheBreadcrumbs from './components/TheBreadcrumbs.vue'
</script>
