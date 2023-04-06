<template>
  <div class="main-app">
    <AppElevatorShaft :elevators="elevators" :numberOfFloors="numberOfFloors" :floorHeight="floorHeight"/>
    <AppFloorButtons :numberOfFloors="numberOfFloors" :isActive="isActive" :isRed="isRed" @callElevator="callElevator"/>
  </div>
</template>

<script>
import AppElevatorShaft from "./components/AppElevatorShaft.vue";
import AppFloorButtons from "./components/AppFloorButtons.vue";
import config from "./config";

export default {
  components: { AppElevatorShaft, AppFloorButtons },
  data() {
    return {
      numberOfFloors: config.numberOfFloors,
      floorHeight: 80,
      elevators: JSON.parse(localStorage.getItem('elevators')) || [],
      elevatorQueue: []
    };
  },
  created() {
    // количество лифтов изменяется в config.js
    const numberOfElevators = config.numberOfElevators;
    if (this.elevators.length === 0) {
      for (let i = 0; i < numberOfElevators; i++) {
        this.elevators.push({
          currentFloor: 1,
          queue: [],
          moving: false,
          targetFloor: null,
          direction: null
        });
      }
    }
  },
  methods: {
    callElevator(floor) {
      if (this.elevators.some(elevator => elevator.currentFloor === floor || elevator.queue.includes(floor))) {
        return;
      }
      const availableElevators = this.elevators.filter(elevator => !elevator.moving);
      if (availableElevators.length === 0) {
        this.elevatorQueue.push(floor);
        return;
      }
      const closestElevator = this.getClosestElevator(floor, availableElevators);
      this.moveElevator(floor, closestElevator);
    },
    getClosestElevator(floor, availableElevators) {
      let closestElevator = null;
      let minimumDistance = Number.MAX_SAFE_INTEGER;
      availableElevators.forEach(elevator => {
        const distance = Math.abs(elevator.currentFloor - floor);
        if (distance < minimumDistance) {
          closestElevator = elevator;
          minimumDistance = distance;
        }
      });
      return closestElevator;
    },
    moveElevator(floor, elevator) {
      const timeToMoveWithOneFloor = 50;
      const timeToOpenAndCloseDoors = 3000;
      elevator.moving = true;
      elevator.direction = elevator.currentFloor < floor ? "↑" : "↓";
      elevator.targetFloor = floor;
      const button = document.querySelector(`.floor-buttons button:nth-child(${floor})`);
      button.classList.add("red");
      const floorDifference = Math.abs(floor - elevator.currentFloor);
      setTimeout(() => {
        elevator.currentFloor = floor;
        elevator.queue = elevator.queue.filter(f => f !== floor);
        this.saveElevatorsState();
        setTimeout(() => {
          if (elevator.queue.length) {
            const nextFloor = elevator.queue[0];
            this.moveElevator(nextFloor, elevator);
          } else {
            elevator.moving = false;
            elevator.direction = null;
            elevator.targetFloor = null;
            if (this.elevatorQueue.length) {
              const nextQueueFloor = this.elevatorQueue.shift();
              const closestElevator = this.getClosestElevator(nextQueueFloor, this.elevators);
              this.moveElevator(nextQueueFloor, closestElevator);
            }
          }
        }, timeToOpenAndCloseDoors);
        button.classList.remove("red");
      }, timeToMoveWithOneFloor * floorDifference);
    },
    isActive(floor) {
      return this.elevators.some(elevator => elevator.currentFloor === floor);
    },
    isRed(floor) {
      return this.elevators.some(elevator => elevator.queue.includes(floor));
    },
    saveElevatorsState() {
      localStorage.setItem('elevators', JSON.stringify(this.elevators));
    }
  }
};
</script>

<style>
.main-app {
    display: flex;
    flex-direction: row;
    align-items: stretch;
}
</style>