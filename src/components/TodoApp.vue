<template>
  <div class="min-h-screen bg-gray-100 py-8">
    <div class="max-w-md mx-auto bg-white rounded-lg shadow-lg p-6">
      <h1 class="text-2xl font-bold text-gray-800 mb-6">📝 Todo App</h1>
      
      <!-- Add Todo Form -->
      <div class="flex gap-2 mb-6">
        <input
          v-model="newTodo"
          @keyup.enter="addTodo"
          type="text"
          placeholder="Add a new todo..."
          class="flex-1 px-4 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500"
        />
        <button
          @click="addTodo"
          class="px-4 py-2 bg-blue-500 text-white rounded-lg hover:bg-blue-600 transition-colors"
        >
          Add
        </button>
      </div>
      
      <!-- Todo List -->
      <ul class="space-y-2">
        <li
          v-for="(todo, index) in todos"
          :key="index"
          class="flex items-center gap-3 p-3 bg-gray-50 rounded-lg group"
        >
          <input
            v-model="todo.done"
            type="checkbox"
            class="w-5 h-5 text-blue-500 rounded focus:ring-blue-500"
          />
          <span
            :class="{ 'line-through text-gray-400': todo.done }"
            class="flex-1 text-gray-700"
          >
            {{ todo.text }}
          </span>
          <button
            @click="removeTodo(index)"
            class="opacity-0 group-hover:opacity-100 text-red-500 hover:text-red-700 transition-opacity"
          >
            ✕
          </button>
        </li>
      </ul>
      
      <!-- Empty State -->
      <p v-if="todos.length === 0" class="text-center text-gray-400 py-8">
        No todos yet. Add one above!
      </p>
      
      <!-- Stats -->
      <div v-if="todos.length > 0" class="mt-6 pt-4 border-t border-gray-200 text-sm text-gray-500">
        {{ doneCount }} of {{ todos.length }} completed
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const newTodo = ref('')
const todos = ref([])

const addTodo = () => {
  if (newTodo.value.trim()) {
    todos.value.push({
      text: newTodo.value.trim(),
      done: false
    })
    newTodo.value = ''
  }
}

const removeTodo = (index) => {
  todos.value.splice(index, 1)
}

const doneCount = computed(() => todos.value.filter(t => t.done).length)
</script>
