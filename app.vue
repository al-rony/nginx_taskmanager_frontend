<script setup>
import { ref, reactive, onMounted } from 'vue';

const title = ref('');
const description = ref('');
const tasks = reactive([]);
onMounted(getAllTasks);

async function addTask() {
  console.log('Task added:', title.value, description.value);

  try {
    const response = await fetch('http://localhost.taskmanager.api:80/api/tasks', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({ title: title.value, description: description.value }),
    });

    if (response.ok) {
      title.value = '';
      description.value = '';
      getAllTasks();
    } else {
      console.error('Failed to add task:', response.statusText);
    }
  } catch (error) {
    console.error('Error adding task:', error);
  }
}

async function getAllTasks() {
  try {
    const response = await fetch('http://localhost.taskmanager.api:80/api/tasks');
    const data = await response.json();
    tasks.splice(0, tasks.length, ...data);
  } catch (error) {
    console.error('Error fetching tasks:', error);
  }
}
</script>

<template>
  <div class="flex">
    <div class="w-1/3 border-r">
      <div class="p-4">
        <h2 class="text-xl font-bold mb-4">Add Task</h2>
        <form @submit.prevent="addTask">
          <div class="mb-4">
            <label for="title" class="block font-semibold">Title*</label>
            <input required v-model="title" id="title" type="text" class="w-full px-4 py-2 border rounded-lg">
          </div>
          <div class="mb-4">
            <label for="description" class="block font-semibold">Description*</label>
            <textarea required v-model="description" id="description" class="w-full px-4 py-2 border rounded-lg"></textarea>
          </div>
          <button type="submit" class="bg-blue-500 text-white px-4 py-2 rounded-lg">Add Task</button>
        </form>
      </div>
    </div>

    <div class="w-2/3">
      <div class="p-4" style="height: calc(100vh - 64px); overflow-y: auto;">
        <h2 class="text-xl font-bold mb-4">All Tasks</h2>
        <ul>
          <li v-for="(task, index) in tasks" :key="index" class="mb-4 p-4 border rounded-lg">
            <h3 class="text-lg font-semibold">{{ task.title }}</h3>
            <p>{{ task.description }}</p>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>