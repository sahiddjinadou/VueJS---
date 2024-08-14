<script setup lang="ts">
import FancyButton from './FancyButton.vue';
import BaseLayout from './BaseLayout.vue';
import { provide } from 'vue';

defineProps<{ msg: string }>()
provide("test","bonjour mon petit fils")
</script>

<template>
  <BaseLayout>
    <template #header='up'>
      <!-- contenu pour le slot header -->
      je suis dans l'entête : {{ up.count }}
    </template>
    <template #default>
      je suis dans le main
    </template>
    <template #footer>
      je suis dans le footer
    </template>
  </BaseLayout>
  <FancyButton v-slot="slotProps12" test="depuis l'ancetre">
    {{ slotProps12.text }}
    <!-- à l'aide de la destructuration -->
  </FancyButton>
  <FancyButton v-slot="{text,count}">
    {{ text }} {{ count }}
  </FancyButton>



</template>

<style scoped></style>
