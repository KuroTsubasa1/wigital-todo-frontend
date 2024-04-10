<template>
  <div>
    <form @submit.prevent="editTodo">
      <input v-model="editedTodo.Description" type="text" placeholder="Edit todo" class="input input-bordered mb-4">
      <select v-model="editedTodo.Status" class="select select-bordered w-full mb-4">
        <option value="0">Active</option>
        <option value="1">Completed</option>
      </select>
      <button type="submit" class="btn btn-primary">Save</button>
      <button type="button" @click="cancelEdit" class="btn btn-secondary">Cancel</button>
    </form>
  </div>
</template>

<script setup lang="ts">

const props = defineProps({
  todo: Object
});

const cancelEdit = () => {
  editedTodo.value = { ...props.todo };
  props.todo.editing = false;
};

const editedTodo = ref({...props.todo});

const emit = defineEmits(['update']);

const editTodo = () => {
  fetch(`https://todobackend.ddev.site/task/${editedTodo.value.id}`, {
    method: 'PUT',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify(editedTodo.value),
  })
      .then(response => response.json())
      .then(updatedTodo => {
        emit('update', updatedTodo.task);
      });
};
</script>