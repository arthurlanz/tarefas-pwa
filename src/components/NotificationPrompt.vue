<template>
  <Transition name="slide-up">
    <div v-if="visible" class="notification-prompt">
      <div class="prompt-content">
        <div class="prompt-text">
          <strong>Ativar notificações?</strong>
          <p>
            Seja avisado quando tarefas forem criadas ou atualizadas em outros
            dispositivos.
          </p>
        </div>
      </div>
      <div class="prompt-actions">
        <button class="btn-allow" @click="allow">Ativar</button>
        <button class="btn-dismiss" @click="dismiss">Agora não</button>
      </div>
    </div>
  </Transition>
</template>

<script setup>
import { onMounted, ref } from "vue";
import { useAuthStore } from "../stores/auth";

const visible = ref(false);
const authStore = useAuthStore();

onMounted(() => {
  if (
    authStore.isAuthenticated &&
    "Notification" in window &&
    Notification.permission === "default" && //
    !localStorage.getItem("push_prompt_dismissed")
  ) {
    setTimeout(() => {
      visible.value = true;
    }, 2000); //
  }
});

async function allow() {
  visible.value = false;
  const granted = await authStore.requestPermission();
  if (granted) {
    const reg = await navigator.serviceWorker.ready;
    await authStore.subscribe(reg); //
  }
}

function dismiss() {
  visible.value = false;
  localStorage.setItem("push_prompt_dismissed", "1"); //
}
</script>

<style scoped>
.notification-prompt {
  position: fixed;
  bottom: 1rem;
  left: 50%;
  transform: translateX(-50%);
  width: min(480px, calc(100vw - 2rem));
  background: #fff;
  border: 1px solid #d6d6d6;
  border-radius: 7px;
  padding: 1rem;
  z-index: 1000;
  display: flex;
  flex-direction: column;
  gap: 0.75rem;
}

.prompt-content {
  display: flex;
  align-items: flex-start;
  gap: 0.75rem;
}

.prompt-text strong {
  display: block;
  color: #222;
  font-size: 0.9rem;
}

.prompt-text p {
  margin: 0.25rem 0 0;
  color: #666;
  font-size: 0.8rem;
  line-height: 1.4;
}

.prompt-actions {
  display: flex;
  gap: 0.5rem;
  justify-content: flex-end;
}

.btn-allow {
  padding: 0.5rem 1rem;
  color: #fff;
  background: #2563eb;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  font-size: 0.8rem;
  font-weight: 600;
}

.btn-allow:hover {
  background: #1d4ed8;
}

.btn-dismiss {
  padding: 0.5rem 1rem;
  background: transparent;
  color: #555;
  border: 1px solid #c7c7c7;
  border-radius: 5px;
  cursor: pointer;
  font-size: 0.8rem;
  font-weight: 600;
}

.btn-dismiss:hover {
  color: #222;
  background: #f3f4f6;
}

/* Animação slide-up */
.slide-up-enter-active,
.slide-up-leave-active {
  transition: all 0.3s ease;
}
.slide-up-enter-from,
.slide-up-leave-to {
  opacity: 0;
  transform: translateX(-50%) translateY(1rem);
}
</style>
