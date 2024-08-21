<template>
  <div
    class="draggable-container"
    :style="containerStyle"
    @mousedown="startDrag"
  >
    <iframe
      class="draggable-iframe"
      :src="iframeSrc"
    ></iframe>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue';

// URL of the website to be displayed in the iframe
const iframeSrc = ref("https://www.youtube.com/embed/dQw4w9WgXcQ");

const dragging = ref(false);
const offsetX = ref(0);
const offsetY = ref(0);
const containerPosition = ref({ top: 100, left: 100 });

const startDrag = (event: MouseEvent) => {
  dragging.value = true;
  offsetX.value = event.clientX - containerPosition.value.left;
  offsetY.value = event.clientY - containerPosition.value.top;

  window.addEventListener('mousemove', drag);
  window.addEventListener('mouseup', stopDrag);
};

const drag = (event: MouseEvent) => {
  if (!dragging.value) return;

  const newLeft = event.clientX - offsetX.value;
  const newTop = event.clientY - offsetY.value;

  // Get window dimensions
  const windowWidth = window.innerWidth;
  const windowHeight = window.innerHeight;

  // Calculate the boundaries
  const containerWidth = 560; // Width of the iframe container
  const containerHeight = 315; // Height of the iframe container

  // Restrict the new position within the window boundaries
  containerPosition.value.left = Math.min(Math.max(0, newLeft), windowWidth - containerWidth);
  containerPosition.value.top = Math.min(Math.max(0, newTop), windowHeight - containerHeight);
};

const stopDrag = () => {
  dragging.value = false;
  window.removeEventListener('mousemove', drag);
  window.removeEventListener('mouseup', stopDrag);
};

const containerStyle = computed(() => ({
  top: `${containerPosition.value.top}px`,
  left: `${containerPosition.value.left}px`,
  position: 'absolute',
  width: '560px',
  height: '315px',
  cursor: 'move',
}));

</script>

<style scoped>
.draggable-container {
  /* Additional styles can be added here if needed */
}

.draggable-iframe {
  width: 100%;
  height: 100%;
  border: none;
}
</style>