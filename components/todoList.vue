<template>
  <div>
    <h1 class="text-4xl font-bold mb-4">Todos</h1>
    <input v-model="newTodo" type="text" placeholder="New todo" class="input input-bordered mb-4">
    <button @click="addTodo" class="btn btn-primary mb-4">Add new Todo</button>

    <ul>
      <li v-for="(todo, index) in todos" :key="index" class="card bordered mb-2">
        <div class="card-body">
          <div class="grid grid-cols-2">
            <div :class="{'text-green-500': todo.Status === 0, 'text-red-500': todo.Status !== 0}">
              {{ todo.Status === 0 ? 'Active' : 'Completed' }}
            </div>
            <div class="text-right">
              {{ todo.Description }}
            </div>
          </div>
        </div>
      </li>
    </ul>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue';

const todos = ref([]);
const newTodo = ref();

const baseUrl = 'https://todobackend.ddev.site/task/';



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
      TaskID: 0,
      UserID: 0,
      Description: newTodo.value,
      Status: 0,
    }),
  })
    .then(response => response.json())
    .then(data => {
      todos.value.push(data);
      newTodo.value = '';
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
h1 {
  color: #333;
}

ul {
  list-style-type: none;
}

li {
  margin: 10px 0;
}
</style>