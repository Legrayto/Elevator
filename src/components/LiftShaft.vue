<script setup>
import LiftCabin from '@/components/LiftCabin';
import { reactive, ref, watch } from 'vue';

const props = defineProps({
  floorCount: Number,
  liftCount: Number,
  nextFloor: Number,
});

const queueData = ref([]);
const liftToFloor = reactive({ id: null, nextFloor: null });

const emit = defineEmits(['setQueue']);

watch(() => props.nextFloor, sendLiftToFloor);

function setLiftData(liftData) {
  if (!queueData.value.includes(liftData)) {
    queueData.value.push(liftData);
  }
  let newQueue = [];
  queueData.value.forEach((lift) => {
    newQueue = [...newQueue, ...lift.queue];
  });
  emit('setQueue', newQueue);
}

function chooseLift(liftsArr, targetFloor) {
  let sortLifts = liftsArr.slice();

  for (let lift of sortLifts) {
    if (
      lift.queue.includes(targetFloor) ||
      targetFloor === lift.currentFloor ||
      targetFloor === lift.nextFloor
    ) {
      liftToFloor.id = null;
      return;
    }
  }

  sortLifts.sort((a, b) => {
    return (
      a.queue.length - b.queue.length ||
      Math.abs(a.nextFloor - targetFloor) - Math.abs(b.nextFloor - targetFloor)
    );
  });

  liftToFloor.id = sortLifts[0].id;
}

function sendLiftToFloor() {
  liftToFloor.nextFloor = props.nextFloor;
  chooseLift(queueData.value, props.nextFloor);
}
</script>

<template>
  <div class="floor-shaft" v-for="lift in liftCount" :key="lift">
    <div v-for="floor in floorCount" :key="floor">
      <LiftCabin
        v-if="floor === 1"
        :data="liftToFloor"
        :lift-id="lift"
        @setLift="setLiftData"
      />
      <span>{{ floor }}</span>
    </div>
  </div>
</template>

<style lang="scss">
.floor-shaft {
  margin-right: 10px;
  height: fit-content;
  display: flex;
  flex-direction: column-reverse;
  background-color: var(--dark-900);
  border-radius: 4px;
  overflow: hidden;
  border: 1px solid var(--dark-100);

  & > div {
    border: 1px solid var(--dark-100);
    padding: 10px;
    width: 100px;
    height: 100px;
    position: relative;
    color: var(--text-primary);

    & > span {
      position: relative;
      z-index: 1;
    }
  }
}
</style>
