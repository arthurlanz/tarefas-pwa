<template>
  <div class="home-view">
    <header class="page-heading">
      <h2>Minhas tarefas</h2>
      <p>Adicione e acompanhe suas atividades.</p>
    </header>

    <p v-if="store.error" class="error-message">{{ store.error }}</p>

    <TaskForm
      :editing-task="editingTask"
      @add="handleAdd"
      @update="handleUpdate"
      @cancel="handleCancel"
    />

    <p v-if="store.loading" class="loading-message">Carregando tarefas...</p>

    <template v-else>
      <section v-if="store.pendingTasks.length > 0" class="task-group">
        <h3 class="section-title">Pendentes ({{ store.pendingTasks.length }})</h3>
        <TaskItem
          v-for="task in store.pendingTasks"
          :key="task.id"
          :task="task"
          @toggle="handleToggle"
          @remove="handleRemove"
          @edit="handleEdit"
        />
      </section>

      <section v-if="store.completedTasks.length > 0" class="task-group">
        <h3 class="section-title">Concluídas ({{ store.completedTasks.length }})</h3>
        <TaskItem
          v-for="task in store.completedTasks"
          :key="task.id"
          :task="task"
          @toggle="handleToggle"
          @remove="handleRemove"
          @edit="handleEdit"
        />
      </section>

      <p v-if="store.tasks.length === 0" class="empty-message">
        Nenhuma atividade cadastrada. Use o formulário acima para adicionar uma tarefa.
      </p>
    </template>

    <InstallButton />
  </div>
</template>

<script setup>
import { onMounted, ref } from "vue";
import TaskForm from "../components/TaskForm.vue";
import TaskItem from "../components/TaskItem.vue";
import InstallButton from "../components/InstallButton.vue";
import { useTasksStore } from "../stores/tasks.js";

const store = useTasksStore();
const editingTask = ref(null);

onMounted(() => {
  store.fetchTasks();
});

function handleAdd(payload) {
  store.addTask(payload);
}

function handleUpdate(id, payload) {
  store.updateTask(id, payload);
  editingTask.value = null;
}

function handleCancel() {
  editingTask.value = null;
}

function handleEdit(task) {
  editingTask.value = task;
}

function handleToggle(id) {
  store.toggleTask(id);
}

function handleRemove(id) {
  if (editingTask.value?.id === id) editingTask.value = null;
  store.removeTask(id);
}
</script>

<style scoped>
.home-view {
  padding-bottom: 30px;
}

.page-heading {
  margin-bottom: 18px;
}

.page-heading h2 {
  margin: 0 0 5px;
  font-size: 1.4rem;
}

.page-heading p {
  margin: 0;
  color: #666;
  font-size: 0.9rem;
}

.task-group {
  margin-top: 22px;
}

.section-title {
  margin: 0 0 10px;
  padding-bottom: 6px;
  color: #333;
  border-bottom: 2px solid #2563eb;
  font-size: 0.95rem;
}

.empty-message,
.loading-message {
  padding: 18px;
  color: #666;
  background: white;
  border: 1px solid #d6d6d6;
  border-radius: 6px;
  text-align: center;
  font-size: 0.88rem;
}

.error-message {
  padding: 9px 11px;
  color: #b91c1c;
  background: #fef2f2;
  border: 1px solid #fecaca;
  border-radius: 5px;
  font-size: 0.85rem;
}
</style>
