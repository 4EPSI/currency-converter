<template>
  <div class="converter">
    <h1>Валютный конвертер</h1>

    <div class="form">
      <input v-model="amount" type="number" placeholder="Сумма" />

      <select v-model="fromCurrency">
        <option value="USD">USD</option>
        <option value="EUR">EUR</option>
        <option value="RUB">RUB</option>
      </select>

      <select v-model="toCurrency">
        <option value="USD">USD</option>
        <option value="EUR">EUR</option>
        <option value="RUB">RUB</option>
      </select>

      <button @click="convert">Конвертировать</button>
    </div>

    <div class="result" v-if="result !== null">
      <p>{{ amount }} {{ fromCurrency }} = {{ result }} {{ toCurrency }}</p>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';

const amount = ref<number | null>(null)
const fromCurrency = ref('USD')
const toCurrency = ref('EUR')
const result = ref<number | null>(null)


const convert = async () => {
  if (!amount.value) return

  try {
    const res = await fetch(`https://api.exchangerate.host/convert?from=${fromCurrency.value}&to=${toCurrency.value}&amount=${amount.value}`)
    const data = await res.json()
    result.value = data.result
  } catch (error) {
    console.error('Ошибка при получении курса:', error)
  }
}
</script>