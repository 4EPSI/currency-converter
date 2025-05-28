<template>
  <div class="demo-wrapper">
    <div class="controls">
      <label>
        Значение прогресса: {{ progress }}%
        <input type="range" min="0" max="100" v-model.number="progress" />
      </label>

      <label>
        Статус:
        <select v-model="status">
          <option value="in-progress">In Progress</option>
          <option value="success">Success</option>
          <option value="warning">Warning</option>
          <option value="error">Error</option>
        </select>
      </label>

      <label>
        <input type="checkbox" v-model="isDashboard" /> Режим dashboard
      </label>
    </div>

    <ProgressCircle
      :percentage="progress"
      :status="status"
      :mode="isDashboard ? 'dashboard' : 'default'"
    />
  </div>
</template>

<script setup>
import { ref } from 'vue'
import ProgressCircle from '~/components/ProgressCircle.vue'

const progress = ref(0)
const status = ref('in-progress')
const isDashboard = ref(false)
</script>

<style scoped>
.demo-wrapper {
  padding: 2rem;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 2rem;
  font-family: sans-serif;
}

.controls {
  display: flex;
  flex-direction: column;
  gap: 1rem;
  align-items: flex-start;
}

input[type='range'] {
  width: 300px;
}

select {
  padding: 0.25rem;
  font-size: 1rem;
}
</style>