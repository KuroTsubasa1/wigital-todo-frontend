<template>
  <!-- Main container for the ToDo application -->
  <div class="flex justify-center mt-5 text-white">
    <div>
      <!-- Application title -->
      <h1 class="text-4xl font-bold mb-4 text-center ">Wigital ToDo Frontend</h1>

      <!-- Input field and button for adding new todos -->
      <div class="mt-10 input-container">
        <input v-model="newTodo" type="text" placeholder="Type your ToDo" class="input input-bordered mb-4">
        <button @click="addTodo" class="btn btn-primary mb-4 align-right">
          <i class="fas fa-plus"></i> Add new Todo
        </button>
      </div>

      <!-- List of todos -->
      <ul class="todo-list">
        <li v-for="(todo, index) in todos" :key="index" class="todo-item">
          <!-- Display todo item -->
          <div v-if="!todo.editing" class="todo-content">
            <!-- Display todo status -->
            <div :class="{'active': todo.Status === 0, 'completed': todo.Status !== 0}">
              <i :class="todo.Status === 0 ? 'far fa-circle' : 'fas fa-check-circle'"></i>
              {{ todo.Status === 0 ? 'Active' : 'Completed' }}
            </div>
            <!-- Display todo description -->
            <div class="todo-description">
              {{ todo.Description }}
            </div>
            <!-- Display todo actions -->
            <div class="todo-actions">
              <button @click="deleteTodo(todo.id)" class="btn btn-error">
                <i class="fas fa-trash"></i> Delete
              </button>
              <button @click="todo.editing = true" class="btn btn-warning">
                <i class="fas fa-edit"></i> Edit
              </button>
              <button @click="markAsChecked(todo)" class="btn btn-success" v-if="todo.Status === 0">
                <i class="fas fa-check"></i> Mark as Checked
              </button>
            </div>
          </div>
          <!-- Display edit form for todo -->
          <EditTodo v-else :todo="todo" @update="updateTodo" />
        </li>
      </ul>

    </div>
  </div>
</template>

<script setup lang="ts">
import {ref, onMounted} from 'vue';

// State for the list of todos and the new todo input field
const todos = ref([]);
const newTodo = ref();

// Base URL for the todo backend API
const baseUrl = 'https://todobackend.ddev.site/task/';

// Emit event for updating a todo
const emit = defineEmits(['update']);

// Function for adding a new todo
const addTodo = () => {
  // Send a POST request to the backend API
  fetch(baseUrl, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      UserID: 0,
      Description: newTodo.value,
      Status: 0,
    }),
  })
      .then(response => response.json())
      .then(data => {
        // Add the new todo to the list and clear the input field
        todos.value.push(data.task);
        newTodo.value = '';
      });
};

// Function for updating a todo
const updateTodo = (updatedTodo) => {
  // Find the index of the todo in the list
  const index = todos.value.findIndex(todo => todo.id === updatedTodo.id);
  if (index !== -1) {
    // Replace the todo in the list and set editing to false
    todos.value[index] = updatedTodo;
    todos.value[index].editing = false;
  }
};

// Function for deleting a todo
const deleteTodo = (id: number) => {
  // Send a DELETE request to the backend API
  fetch(`${baseUrl}${id}`, {
    method: 'DELETE',
  })
      .then(() => {
        // Find the index of the todo in the list and remove it
        const index = todos.value.findIndex(todo => todo.id === id);
        if (index !== -1) {
          todos.value.splice(index, 1);
        }
      });
};

// Function for marking a todo as checked
const markAsChecked = (todo) => {
  // Send a PUT request to the backend API
  fetch(`https://todobackend.ddev.site/task/${todo.id}`, {
    method: 'PUT',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      ...todo,
      Status: 1,
    }),
  })
      .then(response => response.json())
      .then(updatedTodo => {
        // Find the index of the todo in the list and replace it with the updated todo
        const index = todos.value.findIndex(t => t.id === updatedTodo.task.id);
        if (index !== -1) {
          todos.value[index] = updatedTodo.task;
        }
      });
};

// Function for loading the initial list of todos from the backend API
onMounted(() => {
  fetch('https://todobackend.ddev.site/task/')
      .then(response => response.json())
      .then(data => {
        // Add each todo to the list
        data.tasks.forEach(task => {
          todos.value.push(task);
        });
      });
});
</script>
<style scoped>

.todo-list {
  list-style-type: none;
  padding: 0;
}

.todo-item {
  margin: 10px 0;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
  max-width: 1200px; /* Add this line */
  margin-left: auto; /* Add this line */
  margin-right: auto; /* Add this line */
}

.todo-content {
  display: grid;
  grid-template-columns: 1fr 3fr 1fr;
  align-items: center;
  gap: 10px;
}

.todo-description {
  font-size: 1.2em;
}

.todo-actions {
  display: flex;
  justify-content: flex-end;
  gap: 10px;
}

.active {
  color: green;
}

.completed {
  color: red;
}
.align-right {
  margin-left: auto;
}

.input-container {
  max-width: 1200px;
  margin-left: auto;
  margin-right: auto;
}

.input-container button {
 width: 100%;
}

.input {
  width: 100%;
}

</style>