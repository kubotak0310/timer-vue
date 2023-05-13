<template>
  <DownCounterV class="counter-app" :limit="60" />
</template>

<script setup lang="ts">
import DownCounterV from './components/DownCounterV.vue';
</script>

<style scoped>
/* sizeを帰る場合はscaleプロパティで変更 */
.counter-app {
  transform-origin: left top 0;
  transform: scale(0.8);
}
</style>
