<script setup>
import { computed, onMounted, reactive, watch } from 'vue';

const props = defineProps({
  data: Object,
  liftId: Number,
});

const lift = reactive({
  id: props.liftId,
  currentFloor: 1,
  nextFloor: 1,
  queue: [],
  isMoving: false,
  isWaiting: false,
  isReady: true,
});

const emit = defineEmits(['setLift']);

onMounted(() => {
  emit('setLift', lift);
});

watch(lift, () => {
  emit('setLift', lift);
});

watch(
  () => props.data.nextFloor,
  () => {
    if (lift.id === props.data.id) {
      lift.queue.push(props.data.nextFloor);
    }
    if (lift.isReady) {
      liftStart();
    }
  }
);

watch(() => [lift.queue, lift.isReady], liftStart);

function liftStart() {
  if (lift.queue.length && lift.isReady) {
    lift.nextFloor = lift.queue[0];
  }
}
function liftStartMoving() {
  lift.isReady = false;
  lift.isMoving = true;
  lift.currentFloor = lift.nextFloor;
}
function liftStartWaiting() {
  lift.isMoving = false;
  lift.isWaiting = true;
  setTimeout(() => {
    liftReady();
  }, 3000);
}
function liftReady() {
  lift.queue.shift();
  lift.isWaiting = false;
  lift.isReady = true;
}

const liftPositionStyle = computed(() => {
  const position = -((lift.nextFloor - 1) * 100);
  const transition = Math.abs(lift.nextFloor - lift.currentFloor);
  return `top: ${position}px; transition: top ${transition}s ease-in-out`;
});

const direction = computed(() => {
  return lift.nextFloor - lift.currentFloor;
});
</script>

<template>
  <div
    class="lift"
    :style="liftPositionStyle"
    :class="{
      'lift--moving': lift.isMoving,
      'lift--waiting': lift.isWaiting,
      'lift--ready': lift.isReady,
    }"
    @transitionstart="liftStartMoving"
    @transitionend="liftStartWaiting"
  >
    <div class="lift__screen">{{ lift.nextFloor }}</div>
    <div>
      <div
        class="lift-arrow lift-arrow--up"
        :class="{
          'lift-arrow--active': direction > 0,
          'lift-arrow--waiting': lift.isWaiting,
        }"
      ></div>
      <div
        class="lift-arrow lift-arrow--down"
        :class="{
          'lift-arrow--active': direction < 0,
          'lift-arrow--waiting': lift.isWaiting,
        }"
      ></div>
    </div>
  </div>
</template>

<style lang="scss">
@mixin blink-animation($side) {
  animation: blink-animation-#{$side} 0.7s infinite;
  -webkit-animation: blink-animation-#{$side} 0.7s infinite;

  @keyframes blink-animation-#{$side} {
    to {
      border-#{$side}: 13px solid var(--error);
    }
  }

  @-webkit-keyframes blink-animation-#{$side} {
    to {
      border-#{$side}: 13px solid var(--black);
    }
  }
}

.lift {
  height: 100px;
  width: 100px;
  position: absolute;
  top: 0;
  left: 0;
  z-index: 1;
  transition: 1s ease-in-out;
  display: flex;
  justify-content: center;
  align-items: center;

  &--moving {
    background-color: var(--info-800);
  }
  &--waiting {
    background-color: var(--warning);
  }
  &--ready {
    background-color: var(--success);
  }

  &__screen {
    width: 30px;
    height: 30px;
    border-radius: 5px;
    background: var(--info-dark);
    display: flex;
    justify-content: center;
    align-items: center;
    margin-right: 5px;
  }
}

.lift-arrow {
  width: 0;
  height: 0;
  margin: 3px 0;
  border-left: 10px solid transparent;
  border-right: 10px solid transparent;

  &--up {
    border-bottom: 13px solid var(--black);

    &.lift-arrow--active {
      border-bottom: 13px solid var(--error);
    }

    &.lift-arrow--waiting {
      @include blink-animation('bottom');
    }
  }

  &--down {
    border-top: 13px solid var(--black);

    &.lift-arrow--active {
      border-top: 13px solid var(--error);
    }

    &.lift-arrow--waiting {
      @include blink-animation('top');
    }
  }
}
</style>
