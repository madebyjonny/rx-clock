<template>
  <div>
    <h1>Clock: round:{{ rounds }}</h1>
    <div>
      <span>{{ formattedTime(minutes) }}</span
      >:<span>{{ formattedTime(seconds) }}</span>
    </div>
  </div>

  <button @click="start">start</button>

  <form @submit="setTime">
    <input name="minutes" type="number" placeholder="minutes" /><br />
    <input name="seconds" type="number" placeholder="seconds" /><br />

    <select name="direction">
      <option value="down" selected>count down</option>
      <option value="up">count up</option></select
    ><br />

    <input
      name="rounds"
      type="number"
      placeholder="rounds"
      min="1"
      value="1"
    /><br />

    <button>set time</button>
  </form>
</template>

<script setup lang="ts">
import { ref } from "vue";

const minutes = ref(0);
const seconds = ref(0);
const rounds = ref(1);
const constantTime = ref(0);
const currentRound = ref(1);
const totalSeconds = ref(0);
const dir = ref(1);
const time = ref(0);
const interval = ref();

const formattedTime = (seconds: number) =>
  seconds < 10 ? "0" + seconds : seconds;

const countUp = () => {
  minutes.value = Math.floor(totalSeconds.value / 60);
  seconds.value = totalSeconds.value % 60;
  totalSeconds.value++;

  if (
    totalSeconds.value > constantTime.value &&
    currentRound.value < rounds.value
  ) {
    totalSeconds.value = 0;
    currentRound.value++;
  }

  if (totalSeconds.value > constantTime.value) {
    totalSeconds.value = constantTime.value;
    clearInterval(interval.value);
  }
};

const countDown = () => {
  minutes.value = Math.floor(time.value / 60);
  seconds.value = time.value % 60;
  time.value--;

  if (time.value < 0 && currentRound.value < rounds.value) {
    time.value = constantTime.value;
    currentRound.value++;
  }

  if (time.value < 0) {
    time.value = 0;
    clearInterval(interval.value);
  }
};

function start() {
  interval.value = setInterval(() => {
    if (dir.value === 1) {
      countUp();
    } else {
      countDown();
    }
  }, 1000);
}

const setTime = (e: Event) => {
  e.preventDefault();
  const form = document.querySelector("form") as HTMLFormElement;
  const formData = new FormData(form);

  const min = formData.get("minutes") as string;
  const secs = formData.get("seconds") as string;
  const direction = formData.get("direction") as string;
  const rnd = formData.get("rounds") as string;

  constantTime.value = parseInt(min) * 60 + parseInt(secs);

  time.value = constantTime.value;
  dir.value = direction === "up" ? 1 : -1;
  rounds.value = parseInt(rnd);
};
</script>
