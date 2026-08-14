<template>
  <div v-if="!isOnline" class="offline-banner">
    Sem conexão. Algumas funções ficarão indisponíveis temporariamente.
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue';
const isOnline = ref(navigator.onLine);

function updateOnlineStatus() {
  isOnline.value = navigator.onLine;
}

onMounted(() => {
  window.addEventListener('online', updateOnlineStatus);
  window.addEventListener('offline', updateOnlineStatus);
});

onUnmounted(() => {
  window.removeEventListener('online', updateOnlineStatus);
  window.removeEventListener('offline', updateOnlineStatus);
});
</script>
<style scoped>
.offline-banner {
  display: flex;
  align-items: center;
  justify-content: center;
  min-height: 34px;
  padding: 8px 16px;
  color: white;
  background: #b91c1c;
  text-align: center;
  font-size: 0.8rem;
  font-weight: 650;
  position: sticky;
  top: 0;
  z-index: 1000;
}

</style>
