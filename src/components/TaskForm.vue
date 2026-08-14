<template>
  <form class="task-form" @submit.prevent="handleSubmit">
    <h3 class="form-title">{{ editingTask ? "Editar tarefa" : "Nova tarefa" }}</h3>

    <div class="task-row">
      <input
        id="task-title"
        v-model="newTask"
        type="text"
        placeholder="Digite uma tarefa"
        class="task-input"
      />

      <button type="submit" class="task-button" :disabled="uploading">
        {{ editingTask ? "Salvar" : "Adicionar" }}
      </button>

      <button
        v-if="editingTask"
        type="button"
        class="task-button-cancel"
        @click="handleCancel"
      >
        Cancelar
      </button>
    </div>

    <div class="image-section">
      <img
        v-if="previewUrl || editingTask?.img_url"
        :src="previewUrl || editingTask?.img_url"
        class="image-preview"
        alt="Imagem da tarefa"
      />

      <label class="image-label" :class="{ disabled: uploading }">
        <span v-if="uploading">Enviando...</span>
        <span v-else>
          {{ previewUrl || editingTask?.img_url ? "Trocar imagem" : "Adicionar imagem" }}
        </span>

        <input
          type="file"
          accept="image/jpeg,image/png"
          capture="environment"
          class="image-input"
          :disabled="uploading"
          @change="handleImageChange"
        />
      </label>

      <span class="image-help">Arquivo JPEG ou PNG</span>
    </div>
  </form>
</template>

<script setup>
import { ref, watch } from "vue";
import tasksApi from "../api/tasksApi.js";

const props = defineProps({
  editingTask: {
    type: Object,
    default: null,
  },
});

const emit = defineEmits(["add", "update", "cancel"]);

const newTask = ref("");
const previewUrl = ref(null);
const imgAttachmentKey = ref(null);
const uploading = ref(false);

watch(
  () => props.editingTask,
  (task) => {
    newTask.value = task ? task.title : "";

    if (previewUrl.value) {
      URL.revokeObjectURL(previewUrl.value);
    }

    previewUrl.value = null;
    imgAttachmentKey.value = null;
  },
);

async function handleImageChange(event) {
  const file = event.target.files[0];

  if (!file) return;

  if (previewUrl.value) {
    URL.revokeObjectURL(previewUrl.value);
  }

  previewUrl.value = URL.createObjectURL(file);
  uploading.value = true;

  try {
    const response = await tasksApi.uploadImage(file);
    imgAttachmentKey.value = response.data.attachment_key;
  } catch (err) {
    console.error("Erro ao fazer upload da imagem", err);
    previewUrl.value = null;
    imgAttachmentKey.value = null;
  } finally {
    uploading.value = false;
  }
}

function handleSubmit() {
  if (!newTask.value.trim()) return;

  const payload = {
    title: newTask.value.trim(),
    imgAttachmentKey: imgAttachmentKey.value,
  };

  if (props.editingTask) {
    emit("update", props.editingTask.id, payload);
  } else {
    emit("add", payload);
  }

  newTask.value = "";

  if (previewUrl.value) {
    URL.revokeObjectURL(previewUrl.value);
  }

  previewUrl.value = null;
  imgAttachmentKey.value = null;
}

function handleCancel() {
  newTask.value = "";

  if (previewUrl.value) {
    URL.revokeObjectURL(previewUrl.value);
  }

  previewUrl.value = null;
  imgAttachmentKey.value = null;

  emit("cancel");
}
</script>

<style scoped>
.task-form {
  padding: 16px;
  margin-bottom: 22px;
  background: white;
  border: 1px solid #d6d6d6;
  border-radius: 7px;
}

.form-title {
  margin: 0 0 12px;
  font-size: 1rem;
}

.task-row {
  display: flex;
  gap: 8px;
}

.task-input {
  flex: 1;
  min-width: 0;
  padding: 10px;
  border: 1px solid #d6d6d6;
  border-radius: 5px;
  font-size: 0.92rem;
}

.task-input:focus {
  outline: 2px solid rgba(37, 99, 235, 0.2);
  border-color: #2563eb;
}

.task-button,
.task-button-cancel {
  padding: 10px 14px;
  border-radius: 5px;
  font-size: 0.86rem;
  font-weight: 600;
  cursor: pointer;
}

.task-button {
  color: white;
  background: #2563eb;
  border: 1px solid #2563eb;
}

.task-button:hover:not(:disabled) {
  background: #1d4ed8;
}

.task-button:disabled {
  opacity: 0.6;
  cursor: not-allowed;
}

.task-button-cancel {
  color: #555;
  background: white;
  border: 1px solid #bdbdbd;
}

.image-section {
  display: flex;
  align-items: center;
  gap: 10px;
  margin-top: 10px;
  padding-top: 10px;
  border-top: 1px solid #e5e5e5;
}

.image-preview {
  width: 50px;
  height: 50px;
  object-fit: cover;
  border: 1px solid #d6d6d6;
  border-radius: 5px;
}

.image-label {
  padding: 7px 10px;
  color: #2563eb;
  background: white;
  border: 1px solid #2563eb;
  border-radius: 5px;
  font-size: 0.8rem;
  cursor: pointer;
}

.image-label.disabled {
  opacity: 0.6;
  cursor: not-allowed;
}

.image-input {
  display: none;
}

.image-help {
  color: #777;
  font-size: 0.75rem;
}

@media (max-width: 600px) {
  .task-row {
    flex-direction: column;
  }

  .task-button,
  .task-button-cancel {
    width: 100%;
  }

  .image-section {
    align-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>
