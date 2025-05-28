  <template>
    <div :class="['progress-container', mode]">
      <svg viewBox="0 0 100 100" class="progress-ring">
        <circle
          class="progress-ring__bg"
          cx="50" cy="50" r="45"
          fill="transparent"
          stroke="#eee"
          stroke-width="10"
        />
        <circle
          class="progress-ring__circle"
          cx="50" cy="50" r="45"
          fill="transparent"
          :stroke="strokeColor"
          stroke-width="10"
          stroke-linecap="round"
          :stroke-dasharray="2 * Math.PI * 45"
          :stroke-dashoffset="strokeOffset"
        />
      </svg>
      <div class="progress-content">
        <template v-if="status === 'success'">✓</template>
        <template v-else-if="status === 'warning'">!</template>
        <template v-else-if="status === 'error'">✗</template>
        <template v-else>{{ percentage }}%</template>
      </div>
    </div>
  </template>

  <script setup>
  import { computed } from 'vue'
  const props = defineProps({
    percentage: {
      type: Number,
      required: true
    },
    status: {
      type: String,
      default: 'in-progress'
    },
    mode: {
      type: String,
      default: 'default'
    }
  })

  const radius = 45
  const circumference = 2 * Math.PI * radius

  const strokeOffset = computed(() => {
    return circumference - (props.percentage / 100) * circumference
  })

  const strokeColor = computed(() => {
    if (props.status === 'success') return '#4caf50'
    if (props.status === 'warning') return '#ffa500'
    if (props.status === 'error') return '#f44336'
    const red = Math.round(255 - 2.55 * props.percentage)
    const green = Math.round(2.55 * props.percentage)
    return `rgb(${red}, ${green}, 0)`
  })
  </script>

  <style scoped>
  .progress-container {
    position: relative;
    width: 120px;
    height: 120px;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .progress-container.dashboard {
    width: 160px;
    height: 100px;
  }

  .progress-ring {
    transform: rotate(-90deg);
    width: 100%;
    height: 100%;
  }

  .progress-ring__circle {
    transition: stroke-dashoffset 0.5s ease, stroke 0.5s ease;
    transform: rotate(0deg);
    transform-origin: 50% 50%;
  }

  .progress-content {
    position: absolute;
    font-size: 1.2em;
    font-weight: bold;
  }

  .progress-container.dashboard .progress-content {
    bottom: 0;
    top: auto;
  }
  </style>