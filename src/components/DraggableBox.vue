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
import CloseButton from '../components/CloseButton.vue';
import type { CSSProperties } from 'vue';

const props = defineProps({
  isVisible: Boolean,
  containerSize: {
    type: Object,
    default: () => ({ width: 1000, height: 500 }) // Default container size
  }
});

const emit = defineEmits(['update:visible']);

const dragging = ref(false);
const offsetX = ref(0);
const offsetY = ref(0);
const boxPosition = ref({ top: 0, left: 0 });
const isLarge = ref(false);
const hasMoved = ref(false);

const smallSizePercentage = { width: 8, height: 30 }; // Small size in percentage
const largeSizePercentage = { width: 15, height: 50 }; // Large size in percentage

const getSizeInPixels = (size: { width: number; height: number }) => ({
  width: (props.containerSize.width * size.width) / 100,
  height: (props.containerSize.height * size.height) / 100
});

const startDrag = (event: MouseEvent) => {
  event.preventDefault();
  dragging.value = true;
  hasMoved.value = false;
  offsetX.value = event.clientX - boxPosition.value.left;
  offsetY.value = event.clientY - boxPosition.value.top;

  window.addEventListener('mousemove', drag);
  window.addEventListener('mouseup', stopDrag);
};

const drag = (event: MouseEvent) => {
  hasMoved.value = true;
  
  const newLeft = event.clientX - offsetX.value;
  const newTop = event.clientY - offsetY.value;

  const boxSize = getSizeInPixels(isLarge.value ? largeSizePercentage : smallSizePercentage);

  boxPosition.value.left = Math.min(Math.max(0, newLeft), props.containerSize.width - boxSize.width);
  boxPosition.value.top = Math.min(Math.max(0, newTop), props.containerSize.height - boxSize.height);
};

const stopDrag = () => {
  window.removeEventListener('mousemove', drag);
  window.removeEventListener('mouseup', stopDrag);

  setTimeout(() => {
    dragging.value = true;
  }, 50);

  if (hasMoved.value) {
    hasMoved.value = false;
  }
};

const toggleSize = () => {
  if (dragging.value) {
    const newSize = isLarge.value ? smallSizePercentage : largeSizePercentage;
    const boxSize = getSizeInPixels(newSize);

    let newTop = boxPosition.value.top;
    let newLeft = boxPosition.value.left;

    if (newTop + boxSize.height > props.containerSize.height) {
      newTop = props.containerSize.height - boxSize.height;
    }
    if (newLeft + boxSize.width > props.containerSize.width) {
      newLeft = props.containerSize.width - boxSize.width;
    }

    newTop = Math.max(newTop, 0);
    newLeft = Math.max(newLeft, 0);

    boxPosition.value = { top: newTop, left: newLeft };

    isLarge.value = !isLarge.value;
  }
};

const handleClose = () => {
  emit('update:visible', false);
};

const boxStyle = computed((): CSSProperties => {
  const boxSize = getSizeInPixels(isLarge.value ? largeSizePercentage : smallSizePercentage);
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
  return isLarge.value ? '/shrink.png' : '/enlarge.png';
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
  top: -15px; /* Adjust based on button size */
  right: -15px;
}
.toggle-button {
  position: absolute;
  bottom: 0;
  left: 50%;
  transform: translateX(-50%);
  cursor: pointer;
  margin-bottom: 5px;
}
.toggle-button img {
  width: 40px;
  height: 40px;
}
</style>