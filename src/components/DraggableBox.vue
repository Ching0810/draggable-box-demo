<template>
  <div
    v-if="isVisible"
    class="draggable-box"
    :style="boxStyle"
    @mousedown="startDrag"
  >
    <CloseButton @close="handleClose" class="close-button-position" />
    <slot></slot>
    <div class="toggle-button" @click="toggleSize">
      <img :src="buttonImage" alt="Toggle Size" />
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, watch } from 'vue';
import CloseButton from '../components/CloseButton.vue'; // Import the close button component
import type { CSSProperties } from 'vue'; // Import CSSProperties type

const props = defineProps({
  isVisible: Boolean, // Define a prop for visibility
  containerSize: {
    type: Object,
    default: () => ({ width: 1000, height: 500 }) // Default container size
  }
});

const emit = defineEmits(['update:visible']); // Define the event to emit

const dragging = ref(false);
const offsetX = ref(0);
const offsetY = ref(0);
const boxPosition = ref({ top: 0, left: 0 });
const isLarge = ref(false);
const hasMoved = ref(false); // Flag to track if dragging happened

const smallSize = { width: 70, height: 140 };
const largeSize = { width: 150, height: 300 };

const startDrag = (event: MouseEvent) => {
  event.preventDefault(); // Prevent default browser behavior
  dragging.value = true;
  hasMoved.value = false; // Reset the move flag
  offsetX.value = event.clientX - boxPosition.value.left;
  offsetY.value = event.clientY - boxPosition.value.top;

  window.addEventListener('mousemove', drag);
  window.addEventListener('mouseup', stopDrag);
};

const drag = (event: MouseEvent) => {
  dragging.value = false

  hasMoved.value = true; // Set the move flag

  const newLeft = event.clientX - offsetX.value;
  const newTop = event.clientY - offsetY.value;

  // Get container dimensions
  const containerWidth = props.containerSize.width;
  const containerHeight = props.containerSize.height;

  // Determine the current box size based on isLarge
  const boxSize = isLarge.value ? largeSize : smallSize;

  // Restrict the new position within the container boundaries
  boxPosition.value.left = Math.min(Math.max(0, newLeft), containerWidth - boxSize.width);
  boxPosition.value.top = Math.min(Math.max(0, newTop), containerHeight - boxSize.height);
};

const stopDrag = (event: MouseEvent) => {
  window.removeEventListener('mousemove', drag);
  window.removeEventListener('mouseup', stopDrag);
  
  setTimeout(() => {
    dragging.value = true;
  }, 50)

  // Prevent click event if dragging occurred
  if (hasMoved.value) {
    hasMoved.value = false; // Reset the move flag
  }
};

let toggleSize = () => {
  if (dragging.value) {
    // Temporarily store the current size
    const currentSize = isLarge.value ? largeSize : smallSize;
    const newSize = isLarge.value ? smallSize : largeSize;

    // Calculate new position if toggled to larger size
    let newTop = boxPosition.value.top;
    let newLeft = boxPosition.value.left;

    // Adjust position if the box would exceed the container
    if (newTop + newSize.height > props.containerSize.height) {
      newTop = props.containerSize.height - newSize.height;
    }
    if (newLeft + newSize.width > props.containerSize.width) {
      newLeft = props.containerSize.width - newSize.width;
    }

    // Ensure position doesn't go negative
    newTop = Math.max(newTop, 0);
    newLeft = Math.max(newLeft, 0);

    boxPosition.value = { top: newTop, left: newLeft };

    // Toggle the size
    isLarge.value = !isLarge.value;
  } else if (!dragging.value) {
    return;
  }
};

const handleClose = () => {
  emit('update:visible', false); // Emit event to update visibility
};

const boxStyle = computed((): CSSProperties => {
  const boxSize = isLarge.value ? largeSize : smallSize;
  return {
    top: `${boxPosition.value.top}px`,
    left: `${boxPosition.value.left}px`,
    position: 'absolute',
    width: `${boxSize.width}px`,
    height: `${boxSize.height}px`,
    backgroundColor: 'lightblue',
    cursor: 'move',
  };
});

const buttonImage = computed(() => {
  return isLarge.value
    ? '/shrink.png'
    : '/enlarge.png';
});

watch(() => props.isVisible, (newVal) => {
  if (newVal) {
    boxPosition.value = { top: 0, left: 0 };
  }
});
</script>

<style scoped>
.draggable-box {
  position: relative;
}
.close-button-position {
  position: absolute;
  top: -15px; /* Half of the button height */
  right: -15px; /* Half of the button width */
}
.toggle-button {
  position: absolute;
  bottom: 0;
  left: 50%;
  transform: translateX(-50%);
  background: none;
  border: none;
  padding: 0;
  cursor: pointer;
  line-height: 0;
  margin-bottom: 5px;
}
.toggle-button img {
  width: 40px;
  height: 40px;
  object-fit: cover;
}
</style>