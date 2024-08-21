<template>
  <button @click="toggleSize" class="toggle-button">
    <img :src="buttonImage" alt="Toggle Size" />
  </button>
</template>

<script setup lang="ts">
import { computed } from 'vue';
import increase from '../../public/increase.png'
import decrease from '../../public/decrease.png'

// Define the emitted event
const emit = defineEmits(['toggle']);

// Use props to receive the size state from parent
const props = defineProps({
  isLarge: Boolean // Boolean indicating if the box is large
});

const buttonImage = computed(() => {
  return props.isLarge
    ? decrease // Path to image for larger box size
    : increase
});

const toggleSize = () => {
  emit('toggle');
};
</script>

<style scoped>
.toggle-button {
  position: absolute;
  bottom: 0;
  left: 50%;
  transform: translateX(-50%);
  background: none;
  border: none;
  padding: 0;
  cursor: pointer;
}

.toggle-button img {
  width: 30px; /* Adjust size as needed */
  height: 30px; /* Adjust size as needed */
}
</style>