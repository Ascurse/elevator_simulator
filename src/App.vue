<template>
  <div class="main" v-for="floor in floors.slice().reverse()" :key="floor.id">
    <div class="floor"></div>
    <div>
      <button
        :class="[{ active: floor.favorited }, { not_active: !floor.favorited }]"
        @click="addToElevatorQueue(floor), (floor.favorited = true)"
      >
        {{ floor.num }}
      </button>
    </div>
  </div>
  <h1>{{ currentFloor }}</h1>
  <h1>{{ elevatorQueue }}</h1>
</template>

<script>
import { ref, watch } from "vue";
export default {
  setup() {
    const floors = [
      { id: 1, num: 1, favorited: false },
      { id: 2, num: 2, favorited: false },
      { id: 3, num: 3, favorited: false },
      { id: 4, num: 4, favorited: false },
      { id: 5, num: 5, favorited: false },
    ];
    let currentFloor = ref(1);
    let elevatorQueue = ref([]);
    let isMoving = ref(false);
    let isMovingUp = ref(false);
    let timer = ref(1);

    async function elevatorMove() {
      if (elevatorQueue.value.length) {
        isMoving.value = true;
        console.log("start!");
        timer.value = Math.abs(currentFloor.value - elevatorQueue.value[0].num);
        isMovingUp.value = elevatorQueue.value[0].num > currentFloor.value;
        await sleep(timer.value * 1000);
        console.log("setting current floor");
        setCurrentFloor(elevatorQueue.value[0].num);
        console.log("set!");
        await sleep(3000);
        console.log("delete");
        elevatorQueue.value[0].favorited = false;
        elevatorQueue.value.shift();
        console.log("del!");
        isMoving.value = false;
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
      },
      { deep: false }
    );

    return {
      floors,
      currentFloor,
      setCurrentFloor,
      elevatorMove,
      elevatorQueue,
      addToElevatorQueue,
    };
  },
  components: {},
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.main {
  display: flex;
  flex-direction: row;
  flex-wrap: nowrap;
}
.floor {
  display: flex;
  border: 1px solid black;
  flex-direction: column-reverse;
  height: 150px;
  width: 150px;
}

.elevator_btn {
  color: white;
  cursor: pointer;
}

.active {
  background-color: red;
}
</style>
