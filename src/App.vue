<template>
  <main>
    <h1>Таймер</h1>
    <div class="clock" v-show="isStarted">
      <div>{{ hourClock }}</div>
      :
      <div>{{ minutesClock }}</div>
      :
      <div>{{ secondsClock }}</div>
    </div>
    <div class="inputs" v-show="!isStarted">
      <div>
        <TimeBlock is-hour :is-disabled="isStarted" @change-value="setHour" />
        часы
      </div>
      <div>
        <TimeBlock :is-disabled="isStarted" @change-value="setMinutes" />
        минуты
      </div>
      <div>
        <TimeBlock :is-disabled="isStarted" @change-value="setSeconds" />
        секунды
      </div>
    </div>
    <div class="buttons">
      <button @click="toggleTimer">{{ startButtonText }}</button>
      <button @click="reset">Сбросить</button>
    </div>
  </main>
</template>

<script setup lang="ts">
import TimeBlock from "./components/TimeBlock.vue";
import { computed, ref } from "vue";

const isStarted = ref(false);
const wasPaused = ref(false);

const time = ref(0);
const timer = ref<ReturnType<typeof setInterval> | null>(null);

const tempTime = ref({
  hours: 0,
  minutes: 0,
  seconds: 0,
});

const hourClock = computed(() => {
  return `${Math.floor(time.value / 3600)}`.padStart(2, "0");
});

const minutesClock = computed(() => {
  return `${Math.floor((time.value % 3600) / 60)}`.padStart(2, "0");
});

const secondsClock = computed(() => {
  return `${Math.floor(time.value % 60)}`.padStart(2, "0");
});

const startButtonText = computed(() => (isStarted.value ? "Пауза" : "Старт"));

const setHour = (value: number) => {
  wasPaused.value = false;
  tempTime.value.hours = value;
};

const setMinutes = (value: number) => {
  wasPaused.value = false;
  tempTime.value.minutes = value;
};

const setSeconds = (value: number) => {
  wasPaused.value = false;
  tempTime.value.seconds = value;
};

const reduceTime = () => {
  if (time.value === 0 && timer.value) {
    clearInterval(timer.value);
    timer.value = null;
    return;
  }

  time.value--;
};

const toggleTimer = () => {
  isStarted.value = !isStarted.value;

  if (isStarted.value) {
    if (!wasPaused.value) {
      time.value =
        tempTime.value.hours * 3600 +
        tempTime.value.minutes * 60 +
        tempTime.value.seconds;
    }

    timer.value = setInterval(reduceTime, 1000);
    return;
  }

  if (timer.value) {
    wasPaused.value = true;
    clearInterval(timer.value);
  }
};

const reset = () => {
  if (timer.value) {
    clearInterval(timer.value);
    timer.value = null;
  }

  time.value = 0;
  wasPaused.value = false;
  isStarted.value = false;
};
</script>

<style scoped>
main {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1.5rem;
}

h1 {
  font-size: 2rem;
}

.clock {
  display: flex;
  gap: 0.25rem;
  font-size: 1.25rem;
}

.inputs {
  display: flex;
  gap: 1.5rem;
  font-size: 1.25rem;
}

.buttons {
  display: flex;
  gap: 1.5rem;
}
</style>
