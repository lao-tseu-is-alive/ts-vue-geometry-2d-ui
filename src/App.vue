<script setup lang="ts">
import "./style/skeleton.css";
import "./style/style.css";
import Flower from './components/Flower.vue'
</script>

<template>
  <div class="container">
    <Flower :initial-petals="5" />
  </div>
</template>

<style scoped>

</style>
