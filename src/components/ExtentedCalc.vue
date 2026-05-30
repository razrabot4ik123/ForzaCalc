<script setup lang="ts">
import { computed, ref } from 'vue'

type Drive = 'RWD' | 'FWD' | 'AWD'
type Surface = 'Road' | 'Rally' | 'Offroad'

const drive = ref<Drive>('RWD')
const surface = ref<Surface>('Road')

const weight = ref(1500)

const frontWeight = ref(54)

const springBase = ref(100)
const springFactor = ref(3)

const bumpPercent = ref(0.65)

const weightOffset = computed(() => frontWeight.value - 50)

const frontSpring = computed(
  () => +(springBase.value + weightOffset.value * springFactor.value).toFixed(1),
)

const rearSpring = computed(
  () => +(springBase.value - weightOffset.value * springFactor.value).toFixed(1),
)

const frontArb = computed(() => +(32.5 + weightOffset.value * 0.65).toFixed(1))

const rearArb = computed(() => +(32.5 - weightOffset.value * 0.65).toFixed(1))

const frontRebound = computed(() => +(11 + weightOffset.value * 0.2).toFixed(1))

const rearRebound = computed(() => +(11 - weightOffset.value * 0.2).toFixed(1))

const frontBump = computed(() => +(frontRebound.value * bumpPercent.value).toFixed(1))

const rearBump = computed(() => +(rearRebound.value * bumpPercent.value).toFixed(1))

const rideHeight = computed(() => {
  switch (surface.value) {
    case 'Road':
      return '2-4 клика от минимума'
    case 'Rally':
      return 'Максимум'
    case 'Offroad':
      return 'Максимум'
  }
})

const tirePressure = computed(() => {
  switch (surface.value) {
    case 'Road':
      return {
        front: 2.0,
        rear: 2.0,
      }

    case 'Rally':
      return {
        front: 1.6,
        rear: 1.6,
      }

    case 'Offroad':
      return {
        front: 1.3,
        rear: 1.3,
      }
  }
})

const differential = computed(() => {
  switch (drive.value) {
    case 'RWD':
      return {
        accel: 65,
        decel: 15,
      }

    case 'FWD':
      return {
        accel: 45,
        decel: 10,
      }

    case 'AWD':
      return {
        frontAccel: 20,
        rearAccel: 60,
        frontDecel: 10,
        rearDecel: 10,
        balance: 75,
      }
  }
})

const brakeBalance = computed(() => {
  switch (drive.value) {
    case 'RWD':
      return 52

    case 'FWD':
      return 54

    case 'AWD':
      return 50
  }
})

const brakePressure = computed(() => 100)

const camberFront = computed(() => -1.5)
const camberRear = computed(() => -1.0)
const caster = computed(() => 7)
</script>

<template>
  <div class="calculator">
    <h1>Forza Horizon 6 Setup Calculator</h1>

    <section>
      <h2>Входные данные</h2>

      <label>
        Привод
        <select v-model="drive">
          <option value="RWD">RWD</option>
          <option value="FWD">FWD</option>
          <option value="AWD">AWD</option>
        </select>
      </label>

      <label>
        Покрытие
        <select v-model="surface">
          <option value="Road">Road</option>
          <option value="Rally">Rally</option>
          <option value="Offroad">Offroad</option>
        </select>
      </label>

      <label>
        Масса
        <input v-model.number="weight" type="number" />
      </label>

      <label>
        Вес спереди %
        <input v-model.number="frontWeight" type="number" min="30" max="70" />
      </label>
    </section>

    <hr />

    <section>
      <h2>Подвеска</h2>

      <p>Пружины перед: {{ frontSpring }}</p>
      <p>Пружины зад: {{ rearSpring }}</p>

      <p>Стабилизатор перед: {{ frontArb }}</p>
      <p>Стабилизатор зад: {{ rearArb }}</p>

      <p>Отбой перед: {{ frontRebound }}</p>
      <p>Отбой зад: {{ rearRebound }}</p>

      <p>Сжатие перед: {{ frontBump }}</p>
      <p>Сжатие зад: {{ rearBump }}</p>

      <p>Клиренс: {{ rideHeight }}</p>
    </section>

    <hr />

    <section>
      <h2>Сход-развал</h2>

      <p>Развал перед: {{ camberFront }}</p>
      <p>Развал зад: {{ camberRear }}</p>

      <p>Схождение перед: 0.1</p>
      <p>Схождение зад: -0.1</p>

      <p>Кастер: {{ caster }}</p>
    </section>

    <hr />

    <section>
      <h2>Шины</h2>

      <p>Давление перед: {{ tirePressure.front }}</p>
      <p>Давление зад: {{ tirePressure.rear }}</p>
    </section>

    <hr />

    <section>
      <h2>Тормоза</h2>

      <p>Баланс: {{ brakeBalance }}%</p>
      <p>Давление: {{ brakePressure }}%</p>
    </section>

    <hr />

    <section>
      <h2>Дифференциал</h2>

      <template v-if="drive === 'RWD'">
        <p>Ускорение: {{ differential.accel }}%</p>
        <p>Торможение: {{ differential.decel }}%</p>
      </template>

      <template v-if="drive === 'FWD'">
        <p>Ускорение: {{ differential.accel }}%</p>
        <p>Торможение: {{ differential.decel }}%</p>
      </template>

      <template v-if="drive === 'AWD'">
        <p>Перед ускорение: {{ differential.frontAccel }}%</p>
        <p>Зад ускорение: {{ differential.rearAccel }}%</p>

        <p>Перед торможение: {{ differential.frontDecel }}%</p>
        <p>Зад торможение: {{ differential.rearDecel }}%</p>

        <p>Баланс: {{ differential.balance }}%</p>
      </template>
    </section>
  </div>
</template>
