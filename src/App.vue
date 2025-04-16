<!-- @format -->

<template>
  <div>
    <h1>Задачи:</h1>
    <div v-if="loading"><p>Загрузка...</p></div>
    <ul v-else-if="tasks.length">
      <li v-for="task in tasks" :key="task.id">
        <label>
          <input
            type="checkbox"
            v-model="task.done"
            v-on:change="updateTasks"
          />
          <h2>{{ task.id }}:</h2>
          <p>{{ task.title }}</p>
        </label>
      </li>
    </ul>
    <div v-else><p>Нет задач</p></div>
  </div>
</template>

<script setup>
import {onMounted, ref} from 'vue'

const tasks = ref([])
const loading = ref(true)

const loadTasks = async () => {
  try {
    const savedTasks = localStorage.getItem('tasks')
    if (!savedTasks) {
      const response = await fetch('/tasks.json')
      if (!response.ok) {
        throw new Error(`Ошибка загрузки файла: ${response.statusText}`)
      }
      tasks.value = await response.json()
      localStorage.setItem('tasks', JSON.stringify(tasks.value))
    } else {
      tasks.value = JSON.parse(savedTasks)
    }
  } catch (error) {
    console.error('Ошибка при загрузке данных:', error)
  } finally {
    loading.value = false
  }
}

const updateTasks = () => {
  localStorage.setItem('tasks', JSON.stringify(tasks.value))
}

onMounted(async () => {
  await loadTasks()
})
</script>

<style scoped>
li {
  list-style: none;
}
label {
  display: flex;
  align-items: center;
}
h2 {
  margin: 0 1% 0 1%;
}
</style>
