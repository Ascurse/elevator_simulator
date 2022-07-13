<template>
  <div class="main" v-for="floor in floors.slice().reverse()" :key="floor.id">
    <div class="floor"></div>
    <div class="button_container">
      <p class="floor_number">{{ floor.num }} этаж</p>
      <span
        class="dot"
        :class="{ active: floor.favorited == true }"
        @click="addToElevatorQueue(floor), (floor.favorited = true)"
      ></span>
    </div>
    <div
      class="elevator"
      :style="elevatorPos"
      :class="{ 'elevator-stop': isStopped }"
    >
      <div v-if="isMoving" class="elevator_info">
        <img
          v-if="isMovingUp"
          class="elevator_info__icon"
          src="./assets/arrowUp.png"
        />
        <img
          v-if="!isMovingUp"
          class="elevator_info__icon"
          src="./assets/arrowDown.png"
        />
        <h3 class="elevator_info__num">{{ elevatorQueue[0].num }}</h3>
      </div>
    </div>
  </div>
  <h3 class="queue" style="display: none">{{ elevatorQueue }}</h3>
</template>

<script>
import { ref, watch, computed } from "vue";

export default {
  setup() {
    const floors = [
      { id: 1, num: 1, favorited: false },
      { id: 2, num: 2, favorited: false },
      { id: 3, num: 3, favorited: false },
      { id: 4, num: 4, favorited: false },
      { id: 5, num: 5, favorited: false },
    ];
    let callFloor = ref(1);
    let currentFloor = ref(1);
    let elevatorQueue = ref([]);
    let isMoving = ref(false);
    let isMovingUp = ref(false);
    let isStopped = ref(false);
    let timer = ref(1);
    let elevatorPos = computed(() => ({
      bottom: 19.5 * callFloor.value - 19.54 + "vh",
      "transition-duration": timer.value + "s",
    }));

    async function elevatorMove() {
      if (elevatorQueue.value.length) {
        isMoving.value = true;
        callFloor.value = elevatorQueue.value[0].num;
        timer.value = Math.abs(currentFloor.value - elevatorQueue.value[0].num);
        isMovingUp.value = elevatorQueue.value[0].num > currentFloor.value;
        await sleep(timer.value * 1000);
        setCurrentFloor(elevatorQueue.value[0].num);
        elevatorQueue.value[0].favorited = false;
        isStopped.value = true;
        await sleep(3000);
        elevatorQueue.value.shift();
        isMoving.value = false;
        isStopped.value = false;
        elevatorMove();
      }
    }

    function addToElevatorQueue(floor) {
      if (
        !elevatorQueue.value.includes(floor) &&
        currentFloor.value !== floor.num
      ) {
        elevatorQueue.value.push(floor);
      }
    }

    function setCurrentFloor(floor) {
      currentFloor.value = floor;
    }

    function sleep(ms) {
      return new Promise((resolve) => {
        setTimeout(resolve, ms);
      });
    }

    watch(
      () => [...elevatorQueue.value],
      (newQueue, oldQueue) => {
        if (newQueue > oldQueue && !isMoving.value) {
          elevatorMove();
        }
      }
    );

    return {
      isMoving,
      isMovingUp,
      floors,
      currentFloor,
      isStopped,
      setCurrentFloor,
      elevatorMove,
      elevatorQueue,
      addToElevatorQueue,
      callFloor,
      elevatorPos,
    };
  },
  components: {},
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
}

.dot {
  height: 25px;
  width: 25px;
  background-color: #bbb;
  border-radius: 50%;
  display: inline-block;
}

.main {
  display: flex;
  flex-direction: row;
  flex-wrap: nowrap;
  align-items: center;
}

.button_container {
  width: 100px;
  margin-left: 20px;
}

.floor_number {
  margin-bottom: 5px;
}

.floor {
  display: flex;
  border-left: 2px solid black;
  border-right: 2px solid black;
  flex-direction: column-reverse;
  height: 19.56vh;
  width: 184px;
}

.elevator {
  background: aqua;
  height: 19.56vh;
  width: 185px;
  position: absolute;
  transition-property: all;
  transition-timing-function: linear;
  margin-bottom: 20px;
  margin-left: 1px;
}

.active {
  background-color: red;
}

.elevator-stop {
  animation-name: elevator;
  animation-iteration-count: 3;
  animation-timing-function: linear;
  animation-duration: 1s;
}
@keyframes elevator {
  from {
    opacity: 1;
  }
  to {
    opacity: 0.1;
  }
}

.elevator_info {
  display: flex;
  flex-direction: row;
  background-color: rgba(255, 0, 0, 0.2);
}

.elevator_info__icon {
  height: 30px;
  width: 30px;
  margin-left: 40px;
  margin-top: 5px;
}

.elevator_info__num {
  margin-left: 5px;
  margin-top: 5px;
}
</style>
