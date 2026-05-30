<script setup lang="ts">
import { ref } from 'vue'

import DefaultCalc from './components/DefaultCalc.vue'
import ExtentedCalc from './components/ExtentedCalc.vue'
import MegaKrutoi from './components/MegaKrutoi.vue'

const typeCalc = ref<'Простой' | 'Расширенный' | 'МегаКрутой'>('Простой')
</script>

<template>
  <select v-model="typeCalc">
    <option>Простой</option>
    <option>Расширенный</option>
    <option>МегаКрутой</option>
  </select>

  <DefaultCalc v-if="typeCalc === 'Простой'" />
  <ExtentedCalc v-if="typeCalc === 'Расширенный'" />
  <MegaKrutoi v-if="typeCalc === 'МегаКрутой'" />
</template>

<style scoped></style>
