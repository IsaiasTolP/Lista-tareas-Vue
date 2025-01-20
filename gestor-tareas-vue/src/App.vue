<template>
  <div id="app">
    <h1>Lista de Tareas</h1>

    <!-- Formulario para agregar tareas -->
    <form @submit.prevent="addTask">
      <input v-model="newTaskName" placeholder="Nombre de la tarea" required />
      <input v-model="newTaskDeadline" type="date" required />
      <button type="submit">Agregar Tarea</button>
    </form>

    <!-- Filtros de tareas -->
    <div>
      <button @click="showCompleted = !showCompleted">
        {{ showCompleted ? 'Mostrar Pendientes' : 'Mostrar Completadas' }}
      </button>
    </div>

    <!-- Lista de tareas -->
    <ul>
      <li v-for="(task, index) in filteredTasks" :key="index">
        <span :class="{ completed: task.completed }">
          {{ task.name }} - Fecha límite: {{ task.deadline }}
        </span>
        <button @click="toggleComplete(index)">
          {{ task.completed ? 'Desmarcar' : 'Completar' }}
        </button>
        <button @click="deleteTask(index)">Eliminar</button>
      </li>
    </ul>

    <!-- Mostrar el total de tareas pendientes -->
    <p>Total de tareas pendientes: {{ pendingTasksCount }}</p>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue';

// Lista reactiva de tareas
const tasks = ref<Array<{ name: string, completed: boolean, deadline: string }>>([]);

// Estado de la nueva tarea
const newTaskName = ref('');
const newTaskDeadline = ref('');

// Mostrar tareas completadas o pendientes
const showCompleted = ref(false);

// Función para agregar una tarea
const addTask = () => {
  if (newTaskName.value.trim() && newTaskDeadline.value) {
    tasks.value.push({
      name: newTaskName.value,
      deadline: newTaskDeadline.value,
      completed: false,
    });
    newTaskName.value = '';
    newTaskDeadline.value = '';
  }
};

// Función para marcar como completada o desmarcar
const toggleComplete = (index: number) => {
  tasks.value[index].completed = !tasks.value[index].completed;
};

// Función para eliminar una tarea
const deleteTask = (index: number) => {
  tasks.value.splice(index, 1);
};

// Propiedad computada para contar tareas pendientes
const pendingTasksCount = computed(() => tasks.value.filter(task => !task.completed).length);

// Propiedad computada para filtrar las tareas mostradas
const filteredTasks = computed(() => {
  return showCompleted.value
    ? tasks.value.filter(task => task.completed)
    : tasks.value.filter(task => !task.completed);
});
</script>

<style>
.completed {
  text-decoration: line-through;
}
</style>
