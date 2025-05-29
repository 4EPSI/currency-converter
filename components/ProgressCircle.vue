<template>
  <div class="progress-bar" :class="{ dashboard: type === 'dashboard' }">
    <svg :width="size" :height="size">
      <circle
        cx="50%"
        cy="50%"
        :r="radius"
        fill="none"
        stroke="#E0E0E0"
        :stroke-width="strokeWidth"
      />
      <circle
        ref="progressCircle"
        cx="50%"
        cy="50%"
        :r="radius"
        fill="none"
        :stroke="currentColor"
        :stroke-width="strokeWidth"
        stroke-linecap="round"
        :stroke-dasharray="circumference"
        :stroke-dashoffset="strokeDashoffset"
        class="progress-circle"
      />
      <text x="50%" y="50%" text-anchor="middle" dy=".3em" class="progress-text">
        {{ Math.round(animatedProgress) }}%
        <tspan v-if="state === 'success'" class="icon">✓</tspan>
        <tspan v-if="state === 'warning'" class="icon">⚠</tspan>
        <tspan v-if="state === 'error'" class="icon">✗</tspan>
      </text>
    </svg>
  </div>
</template>

<script>
import { ref, computed, watch, onMounted } from 'vue';

export default {
  name: 'CircularProgressBar',
  props: {
    progress: {
      type: Number,
      default: 0, // Установлен дефолт, чтобы избежать ошибки
      validator: (value) => value >= 0 && value <= 100,
    },
    state: {
      type: String,
      default: 'in progress',
      validator: (value) => ['in progress', 'success', 'warning', 'error'].includes(value),
    },
    type: {
      type: String,
      default: 'form',
      validator: (value) => ['form', 'dashboard'].includes(value),
    },
    size: {
      type: Number,
      default: 100,
    },
  },
  setup(props) {
    const progressCircle = ref(null);
    const animatedProgress = ref(0);
    const animatedColor = ref({ r: 255, g: 0 });

    const radius = computed(() => (props.size / 2) * 0.8);
    const circumference = computed(() => 2 * Math.PI * radius.value);
    const strokeWidth = computed(() => (props.type === 'dashboard' ? 10 : 5));

    const currentColor = computed(() => {
      if (props.state !== 'in progress') {
        switch (props.state) {
          case 'success':
            return '#32CD32';
          case 'warning':
            return '#FFD700';
          case 'error':
            return '#FF4500';
          default:
            return '#1E90FF';
        }
      }
      return `rgb(${animatedColor.value.r}, ${animatedColor.value.g}, 0)`;
    });

    const strokeDashoffset = computed(() =>
      circumference.value * (1 - animatedProgress.value / 100)
    );

    const animateProgress = (targetProgress) => {
      let startProgress = animatedProgress.value;
      let startTime = null;

      const animate = (timestamp) => {
        if (!startTime) startTime = timestamp;
        const progress = Math.min(1, (timestamp - startTime) / 500);
        animatedProgress.value = startProgress + (targetProgress - startProgress) * progress;

        const targetR = 255 * (1 - targetProgress / 100);
        const targetG = 255 * (targetProgress / 100);
        animatedColor.value.r = Math.round(
          animatedColor.value.r + (targetR - animatedColor.value.r) * progress
        );
        animatedColor.value.g = Math.round(
          animatedColor.value.g + (targetG - animatedColor.value.g) * progress
        );

        if (progress < 1) {
          requestAnimationFrame(animate);
        }
      };
      requestAnimationFrame(animate);
    };

    onMounted(() => {
      const clampedProgress = Math.max(0, Math.min(100, props.progress));
      animatedProgress.value = 0;
      animatedColor.value = { r: 255, g: 0 };
      animateProgress(clampedProgress);
    });

    watch(
      () => props.progress,
      (newProgress) => {
        const clampedProgress = Math.max(0, Math.min(100, newProgress));
        animateProgress(clampedProgress);
      }
    );

    watch(
      () => props.state,
      () => {
        if (progressCircle.value) {
          progressCircle.value.style.transition = 'stroke 0.3s ease';
          setTimeout(() => (progressCircle.value.style.transition = ''), 300);
        }
      }
    );

    return {
      progressCircle,
      radius,
      circumference,
      strokeWidth,
      currentColor,
      strokeDashoffset,
      animatedProgress,
    };
  },
};
</script>

<style scoped>
.progress-bar {
  position: relative;
  display: inline-block;
}
.progress-circle {
  transform: rotate(-90deg);
  transform-origin: center;
  transition: stroke-dashoffset 0.5s ease;
}
.progress-text {
  font-size: 16px;
  font-weight: bold;
  fill: #333;
  font-family: Arial, sans-serif;
}
.icon {
  font-size: 20px;
}
.dashboard .progress-text {
  font-size: 20px;
}
</style>