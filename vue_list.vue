
<template>
  <div id="app" class="todo-container">
    <h1>To-Do List</h1>
    <input
      v-model="newTask"
      placeholder="Добавить задачу"
      @keyup.enter="addTask"
    />
    <button @click="addTask">Добавить</button>

    <ul>
      <li v-for="(task, index) in tasks" :key="task.id" :class="{ done: task.completed }">
        <div class="task-item">
          <input type="checkbox" v-model="task.completed" @change="saveTasks" />
          <span @dblclick="editTask(index)" :contenteditable="task.editing" @blur="saveEdit(index)">
            {{ task.title }}
          </span>
          <button @click="removeTask(index)">Удалить</button>
        </div>
      </li>
    </ul>
  </div>
</template>

<script>
import { ref, onMounted, watch } from 'vue';

export default {
  setup() {
    const newTask = ref('');
    const tasks = ref([]);

    // Загрузка задач из localStorage при загрузке компонента
    onMounted(() => {
      const savedTasks = localStorage.getItem('tasks');
      if (savedTasks) {
        tasks.value = JSON.parse(savedTasks);
      }
    });

    // Следим за изменениями в tasks и сохраняем в localStorage
    watch(tasks, (newTasks) => {
      localStorage.setItem('tasks', JSON.stringify(newTasks));
    }, { deep: true });

    // Добавление новой задачи
    const addTask = () => {
      if (newTask.value.trim() === '') return;
      tasks.value.push({
        id: Date.now(),
        title: newTask.value,
        completed: false,
        editing: false,
      });
      newTask.value = '';
    };

    // Удаление задачи
    const removeTask = (index) => {
      tasks.value.splice(index, 1);
    };

    // Редактирование задачи
    const editTask = (index) => {
      tasks.value[index].editing = true;
    };

    // Сохранение изменений после редактирования
    const saveEdit = (index) => {
      tasks.value[index].editing = false;
      saveTasks();
    };

    // Сохранение задач в localStorage
    const saveTasks = () => {
      localStorage.setItem('tasks', JSON.stringify(tasks.value));
    };

    return {
      newTask,
      tasks,
      addTask,
      removeTask,
      editTask,
      saveEdit,
    };
  },
};
</script>

<style>
.todo-container {
  max-width: 400px;
  margin: 0 auto;
  padding: 20px;
  background-color: #f9f9f9;
  border-radius: 5px;
}

h1 {
  text-align: center;
}

input {
  width: 100%;
  padding: 10px;
  margin-bottom: 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

button {
  padding: 10px 15px;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

button:hover {
  background-color: #0056b3;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 8px 0;
  border-bottom: 1px solid #ddd;
}

li.done span {
  text-decoration: line-through;
}

.task-item {
  display: flex;
  align-items: center;
  gap: 10px;
}

span[contenteditable] {
  border: 1px dashed transparent;
  padding: 2px;
}

span[contenteditable]:focus {
  border-color: #007bff;
}
</style>
