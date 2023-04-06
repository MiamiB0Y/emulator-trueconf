<template>
  <div class="floor-buttons">
    <button
      v-for="i in numberOfFloors"
      :key="i"
      :class="{ active: isActive(i), red: isRed(i) }"
      @click="$emit('callElevator', i)"
    >
      {{ i }}
    </button>
  </div>
</template>

<script>
export default {
  props: {
    numberOfFloors: Number,
    isActive: Function,
    isRed: Function
  }
};
</script>

<style>
.floor-buttons {
  display: flex;
  width: 1rem;
  margin-bottom: 1rem;
  flex-direction: column-reverse;
  justify-content: space-around;
  align-items: flex-end;
}

.floor-buttons button {
  font-size: 1.5rem;
  margin: 0 0.5rem;
  width: 2rem;
  height: 2rem;
  border: none;
  border-radius: 50%;
  background-color: gray;
}

.floor-buttons button.active {
  background-color: #18f;
  color: white;
  border-color: #18f;
}

.floor-buttons button.red {
  background-color: rgb(182, 58, 58);
  color: white;
  border-color: rgb(182, 58, 58);
}
</style>