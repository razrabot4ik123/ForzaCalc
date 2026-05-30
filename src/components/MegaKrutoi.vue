<script setup lang="ts">
import { computed, ref } from 'vue'

type Drive = 'Задний' | 'Передний' | 'Полный'
type Surface = 'Road' | 'Street' | 'Circuit' | 'Touge' | 'Rally' | 'Offroad'
type CarClass = 'B' | 'A' | 'S1' | 'S2' | 'X'

const drive = ref<Drive>('Задний')
const surface = ref<Surface>('Road')
const carClass = ref<CarClass>('A')

const weight = ref(1500)
const frontWeight = ref(54)
const horsePower = ref(400)
const hasAero = ref(true)

const handlingBias = ref<'Grip' | 'Balanced' | 'Race'>('Balanced')

const offset = computed(() => frontWeight.value - 50)

const springBase = computed(() => {
  if (weight.value < 1000) return 75
  if (weight.value < 1200) return 85
  if (weight.value < 1400) return 95
  if (weight.value < 1600) return 105
  return 115
})

const frontSpring = computed(() => +(springBase.value + offset.value * 3).toFixed(1))

const rearSpring = computed(() => +(springBase.value - offset.value * 3).toFixed(1))

const arbBase = computed(() => {
  switch (drive.value) {
    case 'Полный':
      return { front: 20, rear: 50 }
    case 'Передний':
      return { front: 15, rear: 60 }
    default:
      return { front: 25, rear: 35 }
  }
})

const frontARB = computed(() => +(arbBase.value.front + offset.value * 0.65).toFixed(1))

const rearARB = computed(() => +(arbBase.value.rear - offset.value * 0.65).toFixed(1))

const frontRebound = computed(() => +(11 + offset.value * 0.2).toFixed(1))

const rearRebound = computed(() => +(11 - offset.value * 0.2).toFixed(1))

const bumpRatio = computed(() => {
  if (handlingBias.value === 'Grip') return 0.5
  if (handlingBias.value === 'Race') return 0.75
  return 0.65
})

const frontBump = computed(() => +(frontRebound.value * bumpRatio.value).toFixed(1))

const rearBump = computed(() => +(rearRebound.value * bumpRatio.value).toFixed(1))

const tirePressure = computed(() => {
  const base = 1.8 + ((weight.value - 1000) / 1000) * 0.3
  return {
    front: +base.toFixed(2),
    rear: +base.toFixed(2),
  }
})

const alignment = computed(() => {
  switch (surface.value) {
    case 'Circuit':
      return { front: -2.0, rear: -1.5 }
    case 'Touge':
      return { front: -2.2, rear: -1.6 }
    case 'Rally':
    case 'Offroad':
      return { front: -1.0, rear: -0.5 }
    default:
      return { front: -1.5, rear: -1.0 }
  }
})

const brakes = computed(() => {
  let balance = drive.value === 'Передний' ? 54 : drive.value === 'Задний' ? 52 : 50
  if (horsePower.value > 1000) balance += 1
  return { balance, pressure: 100 }
})

const differential = computed(() => {
  if (drive.value === 'Задний') return { accel: 65, decel: 15 }

  if (drive.value === 'Передний') return { accel: 45, decel: 10 }

  return {
    frontAccel: 20,
    rearAccel: 60,
    frontDecel: 10,
    rearDecel: 10,
    balance: 75,
  }
})

const aero = computed(() => {
  if (!hasAero.value) return null

  switch (surface.value) {
    case 'Circuit':
      return { front: 50, rear: 70 }
    case 'Touge':
      return { front: 60, rear: 75 }
    default:
      return { front: 35, rear: 50 }
  }
})

const telemetryFront = ref(75)
const telemetryRear = ref(75)

const telemetryAdvice = computed(() => {
  const messages: string[] = []

  if (telemetryFront.value > 85) messages.push('Передние пружины слишком мягкие')

  if (telemetryFront.value < 55) messages.push('Передние пружины слишком жёсткие')

  if (telemetryRear.value > 85) messages.push('Задние пружины слишком мягкие')

  if (telemetryRear.value < 55) messages.push('Задние пружины слишком жёсткие')

  if (!messages.length) messages.push('Подвеска находится в рабочем диапазоне')

  return messages
})
</script>

<template>
  <div class="wrap">
    <h1>Forza Horizon 6 Advanced Calculator</h1>

    <div class="grid">
      <label
        >Привод
        <select v-model="drive">
          <option>Задний</option>
          <option>Передний</option>
          <option>Полный</option>
        </select>
      </label>

      <label
        >Тип трассы
        <select v-model="surface">
          <option>Road</option>
          <option>Street</option>
          <option>Circuit</option>
          <option>Touge</option>
          <option>Rally</option>
          <option>Offroad</option>
        </select>
      </label>

      <label
        >Класс
        <select v-model="carClass">
          <option>B</option>
          <option>A</option>
          <option>S1</option>
          <option>S2</option>
          <option>X</option>
        </select>
      </label>

      <label
        >Вес (кг)
        <input v-model.number="weight" type="number" />
      </label>

      <label
        >Вес спереди (%)
        <input v-model.number="frontWeight" type="number" />
      </label>

      <label
        >Мощность
        <input v-model.number="horsePower" type="number" />
      </label>
    </div>

    <h2>Пружины</h2>
    <p>Перед: {{ frontSpring }}</p>
    <p>Зад: {{ rearSpring }}</p>

    <h2>Стабилизаторы</h2>
    <p>Перед: {{ frontARB }}</p>
    <p>Зад: {{ rearARB }}</p>

    <h2>Амортизаторы</h2>
    <p>Отбой перед: {{ frontRebound }}</p>
    <p>Отбой зад: {{ rearRebound }}</p>
    <p>Сжатие перед: {{ frontBump }}</p>
    <p>Сжатие зад: {{ rearBump }}</p>

    <h2>Развал</h2>
    <p>Перед: {{ alignment.front }}</p>
    <p>Зад: {{ alignment.rear }}</p>

    <h2>Шины</h2>
    <p>{{ tirePressure.front }} bar / {{ tirePressure.rear }} bar</p>

    <h2>Тормоза</h2>
    <p>Баланс: {{ brakes.balance }}%</p>
    <p>Давление: {{ brakes.pressure }}%</p>

    <h2>Дифференциал</h2>
    <pre>{{ differential }}</pre>

    <h2 v-if="aero">Аэродинамика</h2>
    <pre v-if="aero">{{ aero }}</pre>

    <h2>Телеметрия</h2>

    <label
      >Перед сжатие %
      <input v-model.number="telemetryFront" type="number" />
    </label>

    <label
      >Зад сжатие %
      <input v-model.number="telemetryRear" type="number" />
    </label>

    <ul>
      <li v-for="msg in telemetryAdvice" :key="msg">
        {{ msg }}
      </li>
    </ul>
  </div>
</template>
