<!-- https://github.com/tariibaba/CB-Animated-Countdown-Timer-Vue/blob/main/src/components/AppTimer.vue -->
<template>
  <div class="root">
    <svg class="svg" viewBox="0 0 100 100" xmlns="http://www.w3.org/2000/svg">
      <g class="circle">
        <circle class="time-elapsed-path" cx="50" cy="50" r="45" />
        <path
          class="time-left-path"
          v-if="timeLeft > 0"
          d="
            M 50, 50
            m -45, 0
            a 45,45 0 1,0 90,0
            a 45,45 0 1,0 -90,0
          "
          :style="{ 'stroke-dasharray': strokeDasharray }"
        ></path>
      </g>
    </svg>
    <div class="time-left-container">
      <span class="time-left-label">{{ timeLeftString }}</span>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed, ref } from 'vue';

// import { ref } from 'vue';
// Props
type PropsT = {
  limit: number; //合計時間
};
const props = defineProps<PropsT>();

// reactivity value
const timeElapsed = ref(0);

// 定数
let timerId = 0;

// Method
const padToTwo = (num: number) => {
  // e.g. 4 -> '04'
  return String(num).padStart(2, '0');
};

const startTimer = () => {
  timerId = setInterval(() => {
    // Stop counting when there is no more time left
    if (++timeElapsed.value === props.limit) {
      clearInterval(timerId);
      console.log('=== clear Timer ===');
    }
  }, 1000);
};

startTimer();

const timeLeft = computed(() => {
  return props.limit - timeElapsed.value;
});

// 残り時間
const timeLeftString = computed(() => {
  const minutes = Math.floor(timeLeft.value / 60);
  const seconds = timeLeft.value % 60;
  return `${padToTwo(minutes)}:${padToTwo(seconds)}`;
});

// animationの'stroke-dasharray' の値の計算
const strokeDasharray = computed(() => {
  const radius = 45;
  const totalLength = 2 * Math.PI * radius;
  const timeFraction = timeLeft.value / props.limit;
  const adjTimeFraction = timeFraction - (1 - timeFraction) / props.limit;
  const elapsedDash = Math.floor(adjTimeFraction * totalLength);
  return `${elapsedDash} ${totalLength}`;
});
</script>

<style scoped>
/* Sets the container's height and width */
.root {
  height: 300px;
  width: 300px;
  position: relative;
}

/* Removes SVG styling that would hide the time label */
.circle {
  fill: none;
  stroke: none;
}

/* The SVG path that displays the timer's progress */
.time-elapsed-path {
  stroke-width: 7px;
  stroke: #424242;
}

.time-left-container {
  /* Size should be the same as that of parent container */
  height: inherit;
  width: inherit;

  /* Place container on top of circle ring */
  position: absolute;
  top: 0;

  /* Center content (label) vertically and horizontally  */
  display: flex;
  align-items: center;
  justify-content: center;
}

.time-left-label {
  font-size: 70px;
  font-family: 'Segoe UI';
  color: black;
}

.time-left-path {
  /* Same thickness as the original ring */
  stroke-width: 7px;

  /* Rounds the path endings  */
  stroke-linecap: round;

  /* Makes sure the animation starts at the top of the circle */
  transform: rotate(90deg);
  transform-origin: center;

  /* One second aligns with the speed of the countdown timer */
  transition: 1s linear all;

  /* Colors the ring */
  stroke: blue;
  /* stroke: #409eff; */
}

.svg {
  /* Flips the svg and makes the animation to move left-to-right */
  transform: scaleX(-1);
}
</style>
