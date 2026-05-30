<script setup lang="ts">
import { computed, ref } from 'vue'

const drive = ref<'Задний' | 'Передний' | 'Полный'>('Задний')

const motorDefault = ref(50)

const motorResult = computed(() => Math.abs(motorDefault.value - 50))

const motorOffset = computed(() => motorDefault.value - 50)

const frontSpring = computed(() => 100 + motorOffset.value * 3)
const backSpring = computed(() => 100 - motorOffset.value * 3)

const frontStabilizer = computed(() => 32.5 + 0.65 * motorOffset.value)
const backStabilizer = computed(() => 32.5 - 0.65 * motorOffset.value)

const frontReboundShockAbsorber = computed(() => 11 + 0.2 * motorOffset.value)
const backReboundShockAbsorber = computed(() => 11 - 0.2 * motorOffset.value)

const frontCompressionShockAbsorber = computed(() => frontReboundShockAbsorber.value * 0.5)

const backCompressionShockAbsorber = computed(() => backReboundShockAbsorber.value * 0.5)
</script>

<template>
  <h1>Калькулятор настроек для стрита</h1>

  <h2>Данные, которые нужно выбрать</h2>
  <section>
    <h3>Смещение мотора</h3>
    <label>
      Положение мотора
      <input v-model="motorDefault" type="number" />
    </label>
    <p>Текущее смещение мотора: {{ motorResult }}%</p>
  </section>
  <section>
    <h3>Привод машниы</h3>
    <select v-model="drive">
      <option>Задний</option>
      <option>Передний</option>
      <option>Полный</option>
    </select>
    <p>Текущий привод: {{ drive }}</p>
  </section>
  <hr />
  <h2>Что нужно поставить для машины</h2>
  <section>
    <h3>Пружины</h3>
    <p>Передние: {{ frontSpring }}</p>
    <p>Задние: {{ backSpring }}</p>
  </section>
  <section>
    <h3>Стабилизаторы</h3>
    <p>Передние: {{ frontStabilizer }}</p>
    <p>Задние: {{ backStabilizer }}</p>
  </section>
  <section>
    <h3>Амортизаторы</h3>
    <p>Передние отбой: {{ frontReboundShockAbsorber }}</p>
    <p>Задние отбой: {{ backReboundShockAbsorber }}</p>
    <p>Передние сжатие: {{ frontCompressionShockAbsorber }}</p>
    <p>Задние сжатие: {{ backCompressionShockAbsorber }}</p>
  </section>
  <hr />
  <section>
    <h3>Клиренс</h3>
    <p>3 клика от минимума вправо</p>
  </section>
  <section>
    <h3>Развал-схождение:</h3>
    <article>
      <h4>Развал</h4>
      <p>Передние: -1.5</p>
      <p>Задние: -1</p>
    </article>
    <article>
      <h4>Схождение</h4>
      <p>Передние: 0</p>
      <p>Задние: 0</p>
    </article>
    <article>
      <h4>Угол</h4>
      <p>Угол: 7</p>
    </article>
  </section>
  <section>
    <h3>Шины</h3>
    <article>
      <h4>Давлние</h4>
      <p>Передние: 2.0</p>
      <p>Задние: 2.0</p>
    </article>
  </section>
  <section>
    <h3>Дифференциал</h3>
    <template v-if="drive === 'Передний'">
      <p>Ускорение: 30-60%</p>
      <p>Торможение: 5-15%</p>
    </template>
    <template v-if="drive === 'Задний'">
      <p>Ускорение: 45-65%</p>
      <p>Торможение: 10-25%</p>
    </template>
    <template v-if="drive === 'Полный'">
      <p>Ускорение передние: 15-25%</p>
      <p>Ускорение задние: 45-75%</p>
      <p>Торможение передние: 5-15%</p>
      <p>Торможение задние: 5-15%</p>
      <p>Баланс: 50-85%</p>
    </template>
  </section>
  <section>
    <h3>Передачи</h3>
    <p>Последняя белая полоска упиралась в верхний правый край феолетовой полоски</p>
  </section>
</template>

<style scoped></style>
