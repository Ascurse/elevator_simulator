<template>
  <div class="main" v-for="floor in floors.slice().reverse()" :key="floor.id">
    <div class="floor"></div>
    <button @click="addToElevatorQueue(floor.num)">{{ floor.num }}</button>
  </div>
  <h1>{{ currentFloor }}</h1>
  <h1>{{ elevatorQueue }}</h1>
</template>

<script>
import { ref, watch } from "vue";
export default {
  setup() {
    let currentFloor = ref(1);
    let elevatorQueue = ref([]);

    async function elevatorMove() {
      if (elevatorQueue.value > 0) {
        console.log("start!");
        await setTimeout(() => {
          console.log("setting current floor");
          setCurrentFloor(elevatorQueue.value[0]);
          console.log("set!");
        }, Math.abs(currentFloor.value - elevatorQueue.value[0]) * 1000);
        setTimeout(() => {
          console.log("delete");
          elevatorQueue.value.shift();
          console.log("del!");
        }, 3000);
      }
    }

    function addToElevatorQueue(floor) {
      if (
        !elevatorQueue.value.includes(floor) &&
        currentFloor.value !== floor
      ) {
        elevatorQueue.value.push(floor);
      }
    }

    function setCurrentFloor(floor) {
      currentFloor.value = floor;
    }

    watch(elevatorQueue.value, () => {
      elevatorMove();
    });

    return {
      currentFloor,
      setCurrentFloor,
      elevatorMove,
      elevatorQueue,
      addToElevatorQueue,
    };
  },
  data() {
    return {
      floors: [
        { id: 1, num: 1 },
        { id: 2, num: 2 },
        { id: 3, num: 3 },
        { id: 4, num: 4 },
        { id: 5, num: 5 },
      ],
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
</style>
