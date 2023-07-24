<script setup>
import { ref, watch } from 'vue';

const props = defineProps({
  floorCount: Number,
  liftCount: Number,
  queue: Array,
});

const queue = ref([]);

const emit = defineEmits(['changeFloor']);

function setNextFloor(id) {
  emit('changeFloor', id);
}

function checkQueue() {
  queue.value = props.queue;
}

watch(() => props.queue, checkQueue);
</script>

<template>
  <div class="floors-panel panel">
    <h2 class="panel__title">Вызов лифта на этаж</h2>

    <div class="floors-panel__buttons">
      <button
        v-for="floor in floorCount"
        :key="floor"
        class="floor-button"
        :class="{
          'floor-button--active': queue.includes(floor),
          'floor-button--next': liftCount === 1 && queue.indexOf(floor) === 0,
        }"
        @click="setNextFloor(floor)"
      >
        {{ floor }}
      </button>
    </div>
  </div>
</template>

<style lang="scss">
.floors-panel {
  min-height: 300px;

  &__buttons {
    display: grid;
    grid-template-columns: repeat(2, auto);
    gap: 15px 5px;
    grid-auto-flow: row-reverse;
  }
}

.floor-button {
  width: 30px;
  height: 30px;
  border-radius: 50%;
  border: 1px solid var(--dark-600);
  cursor: pointer;
  user-select: none;
  box-shadow: var(--box-shadow-primary);
  background-color: var(--dark-500);
  color: #fff;
  transition: border 0.1s ease-in, color 0.1s ease-in, transform 0.05s ease;

  &--active {
    border: 2px solid var(--error-dark);
    background-color: var(--error);
  }

  &--next {
    background-color: #209cff;
    border: 2px solid #16d9e3;
  }

  &:active {
    border: 1px solid var(--error);
    color: var(--error);
    transform: scale(80%) translateY(2px);
  }

  &:not(&--active):not(&:active):hover {
    border: 1px solid var(--info);
    color: var(--info);
  }
}
</style>
