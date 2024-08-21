<template>
  <div v-if="isVisible" class="draggable-box" :style="boxStyle" @mousedown="startDrag">
    <CloseButton @close="handleClose" />
    <slot></slot>
    <ToggleButton :isLarge="isLarge" @toggle="toggleSize" />
  </div>
</template>

<script setup lang="ts">
import { ref, computed, defineEmits, defineProps, watch } from 'vue';
import ToggleButton from './ToggleButton.vue';
import CloseButton from './CloseButton.vue'; // Import the close button component
import type { CSSProperties } from 'vue'; // Import CSSProperties type

const props = defineProps({
  isVisible: Boolean, // Define a prop for visibility
  containerSize: Object // Define a prop for container size
});

const emit = defineEmits(['update:visible']); // Define the event to emit

const dragging = ref(false);
const offsetX = ref(0);
const offsetY = ref(0);
const boxPosition = ref({ top: 0, left: 0 });
const isLarge = ref(false);

const startDrag = (event: MouseEvent) => {
  dragging.value = true;
  offsetX.value = event.clientX - boxPosition.value.left;
  offsetY.value = event.clientY - boxPosition.value.top;

  window.addEventListener('mousemove', drag);
  window.addEventListener('mouseup', stopDrag);
};

const drag = (event: MouseEvent) => {
  if (!dragging.value) return;

  const newLeft = event.clientX - offsetX.value;
  const newTop = event.clientY - offsetY.value;

  // Get container dimensions
  const containerWidth = props.containerSize.width;
  const containerHeight = props.containerSize.height;

  // Calculate the boundaries based on the current box size
  const boxWidth = isLarge.value ? 150 : 70; // Width of the box
  const boxHeight = isLarge.value ? 300 : 140; // Height of the box

  // Restrict the new position within the container boundaries
  boxPosition.value.left = Math.min(Math.max(0, newLeft), containerWidth - boxWidth);
  boxPosition.value.top = Math.min(Math.max(0, newTop), containerHeight - boxHeight);
};

const stopDrag = () => {
  dragging.value = false;
  window.removeEventListener('mousemove', drag);
  window.removeEventListener('mouseup', stopDrag);
};

const toggleSize = () => {
  isLarge.value = !isLarge.value;
};

const handleClose = () => {
  emit('update:visible', false); // Emit event to update visibility
};

const boxStyle = computed((): CSSProperties => {
  const width = isLarge.value ? '150px' : '70px'; // Width of the box
  const height = isLarge.value ? '300px' : '140px'; // Height of the box
  return {
    top: `${boxPosition.value.top}px`,
    left: `${boxPosition.value.left}px`,
    position: 'absolute',
    width: width,
    height: height,
    backgroundColor: 'lightblue',
    cursor: 'move',
    overflow: 'hidden', // Ensures the button stays inside the box
  };
});

// Watch for changes to the isVisible prop
watch(() => props.isVisible, (newVal) => {
  if (newVal) {
    // Reset box position when becoming visible
    boxPosition.value = { top: 0, left: 0 };
  }
});
</script>

<style scoped>
.draggable-box {
  position: relative; /* Ensure that the buttons are positioned relative to this box */
}
</style>