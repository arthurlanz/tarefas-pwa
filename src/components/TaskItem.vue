<template>
  <div class="task-item" :class="{ done: task.done }">
    <img
      v-if="task.img_url"
      :src="task.img_url"
      class="task-thumbnail"
      alt="Imagem da tarefa"
    />

    <label class="task-label">
      <input
        type="checkbox"
        :checked="task.done"
        @change="$emit('toggle', task.id)"
      />
      <span class="task-title">{{ task.title }}</span>
    </label>

    <div class="task-actions">
      <button type="button" class="task-edit" @click="$emit('edit', task)">
        Editar
      </button>
      <button type="button" class="task-remove" @click="$emit('remove', task.id)">
        Remover
      </button>
    </div>
  </div>
</template>

<script setup>
defineProps({
  task: {
    type: Object,
    required: true,
  },
});

defineEmits(["toggle", "remove", "edit"]);
</script>

<style scoped>
.task-item {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 11px;
  margin-bottom: 8px;
  background: white;
  border: 1px solid #d6d6d6;
  border-radius: 6px;
}

.task-thumbnail {
  width: 42px;
  height: 42px;
  object-fit: cover;
  border: 1px solid #d6d6d6;
  border-radius: 5px;
}

.task-label {
  display: flex;
  align-items: center;
  flex: 1;
  gap: 10px;
  min-width: 0;
  cursor: pointer;
}

.task-label input {
  width: 18px;
  height: 18px;
  accent-color: #2563eb;
}

.task-title {
  overflow-wrap: anywhere;
  font-size: 0.92rem;
}

.task-item.done {
  background: #f7f7f7;
  opacity: 0.7;
}

.task-item.done .task-title {
  color: #777;
  text-decoration: line-through;
}

.task-actions {
  display: flex;
  gap: 5px;
}

.task-edit,
.task-remove {
  padding: 5px 7px;
  background: transparent;
  border: none;
  font-size: 0.78rem;
  cursor: pointer;
}

.task-edit {
  color: #2563eb;
}

.task-remove {
  color: #b91c1c;
}

.task-edit:hover,
.task-remove:hover {
  text-decoration: underline;
}

@media (max-width: 500px) {
  .task-item {
    align-items: flex-start;
    flex-wrap: wrap;
  }

  .task-actions {
    width: 100%;
    padding-left: 28px;
  }
}
</style>
