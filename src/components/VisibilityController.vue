<template>
  <div class="visibility-controller">
    <button @click="toggleVisibility" class="toggle-button">
      {{ isVisible ? 'Hide' : 'Show' }} Draggable Box
    </button>
    <DraggableBox 
      :isVisible="isVisible" 
      :containerSize="containerSize"
      @update:visible="handleVisibilityChange" />
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue';
import DraggableBox from './DraggableBox.vue'; // Import the draggable box component

const isVisible = ref(true);

const toggleVisibility = () => {
  isVisible.value = !isVisible.value;
};

// Handle the visibility change event from DraggableBox
const handleVisibilityChange = (visible: boolean) => {
  isVisible.value = visible;
};

// Assume containerSize is provided through props or computed
const containerSize = computed(() => ({
  width: 1000, // Replace with actual width
  height: 500 // Replace with actual height
}));
</script>

<style scoped>
.visibility-controller {
  position: relative; /* Ensure the button is positioned relative to this container */
  width: 100%; /* Full width of the container */
  height: 100%; /* Full height of the container */
}

.toggle-button {
  position: absolute; /* Position the button absolutely within the container */
  bottom: 10px; /* Distance from the bottom */
  right: 10px; /* Distance from the right */
  background-color: #007bff;
  color: white;
  border: none;
  padding: 10px;
  cursor: pointer;
}
</style>