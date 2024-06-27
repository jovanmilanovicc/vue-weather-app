<template>

  <div class="flex flex-col min-h-screen font-Roboto bg-weather-primary">
    <Navigation />

    <RouterView v-slot="{ Component }">
      <Transition name="page" mode="out-in">
        <component :is="Component" />
      </Transition>
    </RouterView>
  </div>

</template>

<script setup>
import { RouterView } from 'vue-router';
import Navigation from "@/components/Navigation.vue";
import Button from './components/Button.vue';

</script>

<style>
.page-enter-active {
  transition: 600ms ease all;
}

.page-enter-from {
  opacity: 0;
}
</style>