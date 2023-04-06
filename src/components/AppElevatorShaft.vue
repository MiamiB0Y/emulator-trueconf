<template>
    <div v-for="(elevator, index) in elevators" :key="index" class="elevator-shaft" :style="{height: numberOfFloors * floorHeight + 'px'}">
      <div class="elevator" :style="{ bottom: (elevator.currentFloor - 1) * floorHeight + 'px' }">
        <div v-if="elevator.direction" class="elevator-indicator">{{ elevator.direction }}{{ elevator.targetFloor }}</div>
      </div>
    </div>
</template>

<script>
export default {
  props: {
    elevators: Array,
    numberOfFloors: Number,
    floorHeight: Number
  }
};
</script>

<style>
.elevators {
  display: flex;
  flex-direction: row;
  gap: 20px;
  height: 100%;
}

.elevator-shaft {
  background-image: repeating-linear-gradient(
    transparent,
    transparent 79px,
    black 80px,
    black 80px
  );
  position: relative;
  width: 4rem;
  margin: 0 auto;
  background-color: #ddd;
}

.elevator {
  position: absolute;
  bottom: 0;
  width: 3.9rem;
  height: 4.9rem;
  border: 1px solid black;
  background-color: #56eee6;
  transition: bottom 1s linear;
}

.elevator-indicator {
  position: absolute;
  top: 0;
  font-size: 2rem;
  width: 100%;
  height: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
}
</style>