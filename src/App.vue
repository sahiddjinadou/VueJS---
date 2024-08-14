<script setup lang="ts">
import { ref } from 'vue';
import HelloWorld from './components/HelloWorld.vue';
const message = ref<string>('HelloWorld')

</script>

<template>
<div>
  <HelloWorld v-bind:msg="message" />
</div>
</template>

<style scoped>

</style>
