<script setup>
import FloorsPanel from '@/components/FloorsPanel';
import LiftShaft from '@/components/LiftShaft';
import { ref } from 'vue';
import LiftSettings from '@/components/LiftSettings';

const floorCount = ref(null);
const liftCount = ref(null);
const queue = ref([]);
const nextFloor = ref(null);

function setNextFloor(floor) {
  nextFloor.value = floor;
}

function setFloorCount(value) {
  floorCount.value = value;
}

function setLiftCount(value) {
  liftCount.value = value;
}

function setQueue(queueData) {
  queue.value = queueData;
}
</script>

<template>
  <div class="app">
    <div class="app__info">
      <LiftSettings @changeFloor="setFloorCount" @changeLift="setLiftCount" />
      <FloorsPanel
        :floor-count="floorCount"
        :lift-count="liftCount"
        :queue="queue"
        @changeFloor="setNextFloor"
      />
    </div>
    <LiftShaft
      :floor-count="floorCount"
      :next-floor="nextFloor"
      :lift-count="liftCount"
      @setQueue="setQueue"
    />
  </div>
</template>

<style lang="scss">
:root {
  --black: #000;
  --dark-100: #dae0e714;
  --dark-500: #3b4a59;
  --dark-600: #2f3a46;
  --dark-700: #1f262e;
  --dark-900: #101418;
  --info: #90caf9;
  --info-800: #004c99;
  --info-dark: #0059b2;
  --error: #f44336;
  --error-dark: #d32f2f;
  --warning: #ffa726;
  --success: #66bb6a;
  --box-shadow-primary: 0 1px 2px 0 rgba(0, 0, 0, 0.2),
    0 2px 3px 0 rgba(0, 0, 0, 0.19);
  --background-default: #141a1f;
  --text-primary: #fff;
  --text--secondary: #c5cedb;
}

* {
  box-sizing: border-box;
  margin: 0;
}

body {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  height: 100vh;
  background-color: var(--background-default);
  color: var(--text-primary);
}

.app {
  padding: 20px;
  height: 100%;
  display: flex;

  &__info {
    display: flex;
    flex-direction: column;
    row-gap: 20px;
    margin-right: 40px;
    flex: 0 0 350px;
  }
}

.panel {
  display: flex;
  flex-direction: column;
  align-items: center;
  border: 1px solid var(--dark-600);
  background-color: var(--dark-700);
  padding: 16px;
  text-align: center;
  border-radius: 12px;

  &__title {
    margin-bottom: 15px;
  }
}
</style>
