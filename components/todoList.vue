<template>
  <div class="flex justify-center mt-5 text-white">
    <div>
      <h1 class="text-4xl font-bold mb-4  ">Wigital ToDo Frontend</h1>

      <div class="mt-10">
        <input v-model="newTodo" type="text" placeholder="Type your ToDo" class="input input-bordered mb-4">
        <button @click="addTodo" class="btn btn-primary mb-4">Add new Todo</button>
      </div>

      <ul class="todo-list">
        <li v-for="(todo, index) in todos" :key="index" class="todo-item">
          <div v-if="!todo.editing" class="todo-content">
            <div :class="{'active': todo.Status === 0, 'completed': todo.Status !== 0}">
              <i :class="todo.Status === 0 ? 'far fa-circle' : 'fas fa-check-circle'"></i>
              {{ todo.Status === 0 ? 'Active' : 'Completed' }}
            </div>
            <div class="todo-description">
              {{ todo.Description }}
            </div>
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
          <EditTodo v-else :todo="todo" @update="updateTodo" />
        </li>
      </ul>

    </div>
  </div>
</template>

<script setup lang="ts">
import {ref, onMounted} from 'vue';

const todos = ref([]);
const newTodo = ref();

const baseUrl = 'https://todobackend.ddev.site/task/';

const emit = defineEmits(['update']);


/*

  - GET /task : Get all task
	- GET /task/{TaskId} : Get one task
	- POST /task : Create new task
	- PUT /task/{TaskId} : Update task
	- DELETE /task/{TaskId} : Delete task

 */

// { "id": 1, "TaskID": 0, "UserID": 0, "Description": "Test Task", "Status": 0 }
const addTodo = () => {
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

        todos.value.push(data.task);
        newTodo.value = '';
      });
};

const updateTodo = (updatedTodo) => {
  const index = todos.value.findIndex(todo => todo.id === updatedTodo.id);
  if (index !== -1) {
    todos.value[index] = updatedTodo;
    todos.value[index].editing = false;
  }
};

const deleteTodo = (id: number) => {
  fetch(`${baseUrl}${id}`, {
    method: 'DELETE',
  })
      .then(() => {
        const index = todos.value.findIndex(todo => todo.id === id);
        if (index !== -1) {
          todos.value.splice(index, 1);
        }
      });
};

const markAsChecked = (todo) => {
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
        const index = todos.value.findIndex(t => t.id === updatedTodo.task.id);
        if (index !== -1) {
          todos.value[index] = updatedTodo.task;
        }
      });
};

onMounted(() => {
  // load initial todos from https://todobackend.ddev.site/task/
  fetch('https://todobackend.ddev.site/task/')
      .then(response => response.json())
      .then(data => {
        // {"tasks":[{"id":1,"TaskID":0,"UserID":0,"Description":"Test Task","Status":0}]}

        // loop tasks
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
  max-width: 600px; /* Add this line */
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
</style>