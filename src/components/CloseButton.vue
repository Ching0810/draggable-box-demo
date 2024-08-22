<template>
  <button @click="close" class="close-button">
    <img src="/X.png" alt="Close" />
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
  background-color: transparent;
  border: none;
  padding: 0;
  cursor: pointer;
  width: 30px;
  height: 30px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.close-button img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}
</style>