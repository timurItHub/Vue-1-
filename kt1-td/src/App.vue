<template>
  <div id="app">
    <h1>ToDo List</h1>

    <!-- Форма добавления задач -->
    <form @submit.prevent="addTask">
      <input 
        v-model="newTask" 
        placeholder="Enter a new task" 
        required 
      />
      <button type="submit">Add</button>
    </form>

    <!-- Фильтры задач -->
    <div class="filters">
      <button 
        v-for="filter in filters" 
        :key="filter" 
        @click="setFilter(filter)" 
        :class="{ active: filter === currentFilter }"
      >
        {{ filter }}
      </button>
    </div>

    <!-- Список задач -->
    <ul>
      <li 
        v-for="(task, index) in filteredTasks" 
        :key="index" 
        :class="{ completed: task.completed }"
      >
        <input 
          type="checkbox" 
          v-model="task.completed" 
          @change="updateTaskState(index)"
        />
        <span>{{ task.text }}</span>
        <button @click="removeTask(index)">Delete</button>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  data() {
    return {
      newTask: '',
      tasks: JSON.parse(localStorage.getItem('tasks')) || [], // Загрузка задач из localStorage
      filters: ['All', 'Completed', 'Incomplete'],
      currentFilter: 'All',
    };
  },
  computed: {
    filteredTasks() {
      if (this.currentFilter === 'All') return this.tasks;
      if (this.currentFilter === 'Completed') {
        return this.tasks.filter(task => task.completed);
      }
      return this.tasks.filter(task => !task.completed);
    },
  },
  methods: {
    addTask() {
      if (this.newTask.trim()) {
        this.tasks.push({ text: this.newTask, completed: false });
        this.newTask = '';
        this.saveTasks();
      }
    },
    removeTask(index) {
      this.tasks.splice(index, 1);
      this.saveTasks();
    },
    updateTaskState(index) {
      // Переключение состояния задачи
      this.tasks[index].completed = !this.tasks[index].completed;
      this.saveTasks();
    },
    setFilter(filter) {
      this.currentFilter = filter;
    },
    saveTasks() {
      localStorage.setItem('tasks', JSON.stringify(this.tasks));
    },
  },
};
</script>

<style scoped>
/* Локальные стили */
h1 {
  text-align: center;
}
form {
  display: flex;
  margin-bottom: 1rem;
}
input[type="text"] {
  flex-grow: 1;
  padding: 0.5rem;
  margin-right: 0.5rem;
}
input[type="checkbox"] {
  margin-right: 10px;
}
button {
  padding: 0.5rem;
}
ul {
  list-style: none;
  padding: 0;
}
li {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0.5rem;
  border-bottom: 1px solid #ddd;
}
.completed span {
  text-decoration: line-through;
  color: grey;
}
</style>
