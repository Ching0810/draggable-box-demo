<template>
  <div ref="gameFeed" class="game-feed">
    <VisibilityController :containerSize="containerSize"/>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue';
import VisibilityController from '../components/VisibilityController.vue';

// Ref to the game feed container
const gameFeed = ref<HTMLDivElement | null>(null);

// Computed property to get container size
const containerSize = computed(() => {
  if (gameFeed.value) {
    return {
      width: gameFeed.value.clientWidth,
      height: gameFeed.value.clientHeight
    };
  }
  return { width: 0, height: 0 };
});
</script>

<style scoped>
.game-feed {
  width: 1000px;
  height: 500px;
  background-color: beige;
  position: relative; /* Ensure child components are positioned relative to this container */
}
</style>