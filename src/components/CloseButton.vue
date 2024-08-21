<!-- CloseButton.vue -->
<template>
  <button @click="close" class="close-button">
    &times;
  </button>
</template>

<script setup lang="ts">
const emit = defineEmits(['close']);

const close = () => {
  emit('close');
};
</script>

<style scoped>
.close-button {
  position: absolute;
  top: 10px;
  right: 10px;
  background-color: #ff4d4d;
  color: white;
  border: none;
  padding: 0;
  border-radius: 50%;
  width: 30px;
  height: 30px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  font-size: 20px;
}
</style>