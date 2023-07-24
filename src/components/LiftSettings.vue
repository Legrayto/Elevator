<script setup>
import { onMounted, ref, watch } from 'vue';

const maxLiftCount = 5;
const minLiftCount = 1;
const maxFloorCount = 8;
const minFloorCount = 5;

const floorCount = ref(5);
const liftCount = ref(1);

const emit = defineEmits(['changeFloor', 'changeLift']);

function setFloor(value) {
  if (value > maxFloorCount) {
    value = maxFloorCount;
  }
  if (value < minFloorCount) {
    value = minFloorCount;
  }

  floorCount.value = value;
  emit('changeFloor', value);
}

function setLift(value) {
  if (value > maxLiftCount) {
    value = maxLiftCount;
  }
  if (value < minLiftCount) {
    value = minLiftCount;
  }

  liftCount.value = value;
  emit('changeLift', value);
}

watch(() => [floorCount.value, liftCount.value], saveToLS);

onMounted(() => {
  const floorSaved = getFromLS('floorCount');
  const liftSaved = getFromLS('liftCount');

  if (floorSaved) {
    setFloor(floorSaved);
  }
  if (liftSaved) {
    setLift(liftSaved);
  }
});

function saveToLS() {
  localStorage.setItem('floorCount', floorCount.value);
  localStorage.setItem('liftCount', liftCount.value);
}

function getFromLS(name) {
  const value = localStorage.getItem(name);
  return +value;
}
</script>

<template>
  <div class="lift-settings panel">
    <h2 class="panel__title">Параметры</h2>

    <div class="lift-settings__item">
      <h3>Количество этажей</h3>

      <div class="lift-settings__buttons">
        <button
          class="lift-settings__button"
          @click="setFloor(floorCount - 1)"
          :disabled="floorCount === minFloorCount"
        >
          -
        </button>
        <div class="lift-settings__count">{{ floorCount }}</div>
        <button
          class="lift-settings__button"
          @click="setFloor(floorCount + 1)"
          :disabled="floorCount === maxFloorCount"
        >
          +
        </button>
      </div>
    </div>
    <div class="lift-settings__item">
      <h3>Количество лифтов</h3>
      <div class="lift-settings__buttons">
        <button
          class="lift-settings__button"
          @click="setLift(liftCount - 1)"
          :disabled="liftCount === minLiftCount"
        >
          -
        </button>
        <div class="lift-settings__count">{{ liftCount }}</div>
        <button
          class="lift-settings__button"
          @click="setLift(liftCount + 1)"
          :disabled="liftCount === maxLiftCount"
        >
          +
        </button>
      </div>
    </div>
  </div>
</template>

<style lang="scss">
.lift-settings {
  &__item {
    display: flex;
    flex-direction: column;
    align-items: center;
    color: var(--text--secondary);

    & + & {
      margin-top: 10px;
    }
  }

  &__buttons {
    margin-top: 10px;
    display: flex;
    justify-content: center;
    align-items: center;
  }

  &__button {
    cursor: pointer;
    display: flex;
    justify-content: center;
    align-items: center;
    user-select: none;
    height: 25px;
    width: 40px;
    border-radius: 10px;
    font-size: 1.2rem;
    box-shadow: var(--box-shadow-primary);
    background-color: transparent;
    border: 2px solid var(--text--secondary);
    transition: all 0.2s ease-in;
    color: var(--text--secondary);

    &:hover:enabled {
      border: 2px solid var(--info);
      color: var(--info);
    }

    &:active {
      transform: scale(80%);
    }

    &:disabled {
      border: 2px solid var(--dark-500);
      cursor: default;
      color: var(--dark-500);
    }
  }

  &__count {
    height: 30px;
    line-height: 30px;
    width: 30px;
    text-align: center;
    user-select: none;
    color: var(--text-primary);
  }
}
</style>
