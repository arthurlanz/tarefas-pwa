<template>
  <button v-if="showInstallButton" class="install-button" @click="installApp">
    Instalar aplicativo
  </button>
</template>

<script setup>
import { ref, onMounted } from 'vue';

const showInstallButton = ref(false);
let deferredPrompt = null;
onMounted(() => {
  window.addEventListener('beforeinstallprompt', (event) => {
    // Impede o banner automático do navegador
    event.preventDefault();
    // Armazena o evento para usar depois
    deferredPrompt = event;
    // Mostra o botão customizado
    showInstallButton.value = true;
  });

  window.addEventListener('appinstalled', () => {
    // Esconde o botão quando o app for instalado
    showInstallButton.value = false;
    deferredPrompt = null;
  });
});
async function installApp() {
  if (!deferredPrompt) return;

  // Mostra o prompt de instalação do navegador
  deferredPrompt.prompt();

  // Aguarda a resposta do usuário
  const { outcome } = await deferredPrompt.userChoice;

  if (outcome === 'accepted') {
    showInstallButton.value = false;
  }

  deferredPrompt = null;
}
</script>
<style scoped>
.install-button {
  display: block;
  width: 100%;
  padding: 11px;
  margin-top: 20px;
  color: white;
  background: #2563eb;
  border: none;
  border-radius: 6px;
  font-size: 0.86rem;
  font-weight: 600;
  cursor: pointer;
}

.install-button:hover {
  background: #1d4ed8;
}
</style>
