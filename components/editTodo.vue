<template>
  <!-- The edit form for a todo item -->
  <div class="edit-form">
    <!-- The form for editing a todo item -->
    <form @submit.prevent="editTodo">
      <!-- Input field for editing the description of a todo item -->
      <input v-model="editedTodo.Description" type="text" placeholder="Edit todo" class="input input-bordered mb-4">
      <!-- Select field for editing the status of a todo item -->
      <select v-model="editedTodo.Status" class="select select-bordered w-full mb-4">
        <option value="0">Active</option>
        <option value="1">Completed</option>
      </select>
      <!-- Button for saving the changes to a todo item -->
      <button type="submit" class="btn btn-primary">Save</button>
      <!-- Button for cancelling the edit of a todo item -->
      <button type="button" @click="cancelEdit" class="btn btn-secondary">Cancel</button>
    </form>
  </div>
</template>

<script setup lang="ts">
// Define the props for the component
const props = defineProps({
  todo: Object
});

// Function for cancelling the edit of a todo item
const cancelEdit = () => {
  editedTodo.value = { ...props.todo };
  props.todo.editing = false;
};

// The state for the edited todo item
const editedTodo = ref({...props.todo});

// Define the emits for the component
const emit = defineEmits(['update']);

// Function for editing a todo item
const editTodo = () => {
  // Send a PUT request to the backend API
  fetch(`https://todobackend.ddev.site/task/${editedTodo.value.id}`, {
    method: 'PUT',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify(editedTodo.value),
  })
      .then(response => response.json())
      .then(updatedTodo => {
        // Emit the update event with the updated todo item
        emit('update', updatedTodo.task);
      });
};
</script>

<style scoped>
.edit-form {
  margin: 20px 0;
  padding: 20px;
  border: 1px solid #ccc;
  border-radius: 5px;
  max-width: 600px;
  margin-left: auto;
  margin-right: auto;
  box-shadow: 0 4px 8px 0 rgba(0,0,0,0.2);
}

.input, .select {
  font-size: 1.2em;
}

.btn {
  margin-top: 20px;
  margin-right: 10px;
}
</style>