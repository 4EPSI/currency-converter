<template>
  <div class="app">
    <h1>Демонстрация кругового прогресс-бара</h1>
    <div class="progress-container">
      <CircularProgressBar
        v-for="(item, index) in progressItems"
        :key="index"
        :progress="item.progress"
        :state="item.state"
        :type="progressType"
      />
    </div>
    <div class="controls">
      <button @click="toggleProgressType">Сменить тип: {{ progressType }}</button>
      <button @click="updateProgress">Обновить прогресс</button>
    </div>
  </div>
</template>

<script>
import { ref } from 'vue';
import CircularProgressBar from '../components/ProgressCircle.vue';

export default {
  name: 'App',
  components: { CircularProgressBar },
  setup() {
    const progressType = ref('form');
    const progressItems = ref([
      { progress: 0, state: 'in progress' },
      { progress: 25, state: 'in progress' },
      { progress: 50, state: 'success' },
      { progress: 75, state: 'warning' },
      { progress: 100, state: 'error' },
    ]);

    const toggleProgressType = () => {
      progressType.value = progressType.value === 'form' ? 'dashboard' : 'form';
    };

    const updateProgress = () => {
      progressItems.value = progressItems.value.map((item) => ({
        ...item,
        progress: Math.floor(Math.random() * 101),
      }));
    };

    return { progressType, toggleProgressType, progressItems, updateProgress };
  },
};
</script>

<style>
.app {
  text-align: center;
  padding: 20px;
}
.progress-container {
  display: flex;
  justify-content: center;
  gap: 20px;
  margin: 20px 0;
  flex-wrap: wrap;
}
.controls {
  margin-top: 20px;
}
button {
  margin: 0 10px;
  padding: 10px 20px;
  font-size: 16px;
  cursor: pointer;
}
</style>
