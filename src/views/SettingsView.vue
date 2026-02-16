<template>
  <div class="px-5 pb-32 max-w-lg mx-auto">
    <h2 :class="['text-xl font-bold mb-6', themeClasses.heading]">Settings</h2>
    
    <!-- Appearance Section -->
    <div :class="['backdrop-blur-xl rounded-2xl p-5 border mb-4', themeClasses.card]">
      <h3 :class="['text-lg font-semibold mb-4 flex items-center gap-2', themeClasses.heading]">
        <svg :class="['w-5 h-5', themeClasses.icon]" fill="none" stroke="currentColor" viewBox="0 0 24 24">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M7 21a4 4 0 01-4-4V5a2 2 0 012-2h4a2 2 0 012 2v12a4 4 0 01-4 4zm0 0h12a2 2 0 002-2v-4a2 2 0 00-2-2h-2.343M11 7.343l1.657-1.657a2 2 0 012.828 0l2.829 2.829a2 2 0 010 2.828l-8.486 8.485M7 17h.01" />
        </svg>
        Appearance
      </h3>
      
      <!-- Dark Mode Toggle -->
      <div class="flex items-center justify-between mb-4">
        <div>
          <p :class="['font-medium', themeClasses.text]">Dark Mode</p>
          <p :class="['text-sm', themeClasses.subtext]">Switch between light and dark theme</p>
        </div>
        <button 
          @click="$emit('toggle-theme')"
          :class="['w-14 h-8 rounded-full transition-colors relative', isDark ? 'bg-purple-500' : 'bg-slate-300']"
        >
          <div :class="['w-6 h-6 rounded-full bg-white shadow-md absolute top-1 transition-all', isDark ? 'left-7' : 'left-1']">
            <svg v-if="isDark" class="w-4 h-4 text-slate-800 absolute top-1 left-1" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M20.354 15.354A9 9 0 018.646 3.646 9.003 9.003 0 0012 21a9.003 9.003 0 008.354-5.646z" />
            </svg>
            <svg v-else class="w-4 h-4 text-amber-500 absolute top-1 left-1" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 3v1m0 16v1m9-9h-1M4 12H3m15.364 6.364l-.707-.707M6.343 6.343l-.707-.707m12.728 0l-.707.707M6.343 17.657l-.707.707M16 12a4 4 0 11-8 0 4 4 0 018 0z" />
            </svg>
          </div>
        </button>
      </div>
    </div>

    <!-- Sound Section -->
    <div :class="['backdrop-blur-xl rounded-2xl p-5 border mb-4', themeClasses.card]">
      <h3 :class="['text-lg font-semibold mb-4 flex items-center gap-2', themeClasses.heading]">
        <svg :class="['w-5 h-5', themeClasses.icon]" fill="none" stroke="currentColor" viewBox="0 0 24 24">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15.536 8.464a5 5 0 010 7.072m2.828-9.9a9 9 0 010 12.728M5.586 15H4a1 1 0 01-1-1v-4a1 1 0 011-1h1.586l4.707-4.707C10.923 3.663 12 4.109 12 5v14c0 .891-1.077 1.337-1.707.707L5.586 15z" />
        </svg>
        Sound
      </h3>
      
      <!-- Sound Toggle -->
      <div class="flex items-center justify-between">
        <div>
          <p :class="['font-medium', themeClasses.text]">Sound Effects</p>
          <p :class="['text-sm', themeClasses.subtext]">Play sounds on task completion and actions</p>
        </div>
        <button 
          @click="toggleSound"
          :class="['w-14 h-8 rounded-full transition-colors relative', soundEnabled ? 'bg-emerald-500' : 'bg-slate-300']"
        >
          <div :class="['w-6 h-6 rounded-full bg-white shadow-md absolute top-1 transition-all', soundEnabled ? 'left-7' : 'left-1']" />
        </button>
      </div>
    </div>

    <!-- Data Management Section -->
    <div :class="['backdrop-blur-xl rounded-2xl p-5 border mb-4', themeClasses.card]">
      <h3 :class="['text-lg font-semibold mb-4 flex items-center gap-2', themeClasses.heading]">
        <svg :class="['w-5 h-5', themeClasses.icon]" fill="none" stroke="currentColor" viewBox="0 0 24 24">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 7v10c0 2.21 3.582 4 8 4s8-1.79 8-4V7M4 7c0 2.21 3.582 4 8 4s8-1.79 8-4M4 7c0-2.21 3.582-4 8-4s8 1.79 8 4m0 5c0 2.21-3.582 4-8 4s-8-1.79-8-4" />
        </svg>
        Data Management
      </h3>
      
      <!-- Export Data -->
      <button 
        @click="exportData"
        :class="['w-full py-3 rounded-xl mb-3 flex items-center justify-center gap-2 transition-all', themeClasses.buttonSecondary]"
      >
        <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M7 16a4 4 0 01-.88-7.903A5 5 0 1115.9 6L16 6a5 5 0 011 9.9M15 13l-3-3m0 0l-3 3m3-3v12" />
        </svg>
        Export Data (JSON)
      </button>
      
      <!-- Import Data -->
      <label :class="['w-full py-3 rounded-xl mb-3 flex items-center justify-center gap-2 transition-all cursor-pointer', themeClasses.buttonSecondary]">
        <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M7 16a4 4 0 01-.88-7.903A5 5 0 1115.9 6L16 6a5 5 0 011 9.9M15 13l-3-3m0 0l-3 3m3-3v12" />
        </svg>
        Import Data (JSON)
        <input 
          type="file" 
          accept=".json" 
          class="hidden" 
          @change="importData"
        />
      </label>
      
      <!-- Clear All Data -->
      <button 
        @click="confirmClearData"
        class="w-full py-3 rounded-xl bg-red-500/10 hover:bg-red-500/20 border border-red-500/20 text-red-400 flex items-center justify-center gap-2 transition-all"
      >
        <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16" />
        </svg>
        Clear All Data
      </button>
    </div>

    <!-- Default View Section -->
    <div :class="['backdrop-blur-xl rounded-2xl p-5 border mb-4', themeClasses.card]">
      <h3 :class="['text-lg font-semibold mb-4 flex items-center gap-2', themeClasses.heading]">
        <svg :class="['w-5 h-5', themeClasses.icon]" fill="none" stroke="currentColor" viewBox="0 0 24 24">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6h16M4 10h16M4 14h16M4 18h16" />
        </svg>
        Default View
      </h3>
      
      <p :class="['text-sm mb-3', themeClasses.subtext]">Select which view opens when you start the app</p>
      
      <div class="grid grid-cols-2 gap-2">
        <button 
          v-for="view in views" 
          :key="view.value"
          @click="setDefaultView(view.value)"
          :class="['py-2 px-3 rounded-xl text-sm font-medium transition-all border', 
            defaultView === view.value ? view.activeClass : themeClasses.viewInactive]"
        >
          {{ view.label }}
        </button>
      </div>
    </div>

    <!-- About Section -->
    <div :class="['backdrop-blur-xl rounded-2xl p-5 border', themeClasses.card]">
      <h3 :class="['text-lg font-semibold mb-2', themeClasses.heading]">About</h3>
      <p :class="['text-sm', themeClasses.subtext]">Todo App v1.0</p>
      <p :class="['text-sm', themeClasses.subtext]">Built with Vue 3 + Tailwind CSS</p>
    </div>

    <!-- Clear Data Confirmation Modal -->
    <transition name="modal">
      <div v-if="showClearModal" :class="['fixed inset-0 z-50 flex items-center justify-center backdrop-blur-sm transition-colors', themeClasses.modalOverlay]" @click="showClearModal = false">
        <div :class="['w-full max-w-sm rounded-2xl p-6 border shadow-2xl mx-4 transition-colors', themeClasses.modalBg]" @click.stop>
          <div class="w-12 h-12 rounded-full bg-red-500/20 flex items-center justify-center mx-auto mb-4">
            <svg class="w-6 h-6 text-red-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-3L13.732 4c-.77-1.333-2.694-1.333-3.464 0L3.34 16c-.77 1.333.192 3 1.732 3z" />
            </svg>
          </div>
          <h3 :class="['text-xl font-bold text-center mb-2', themeClasses.heading]">Clear All Data?</h3>
          <p :class="['text-sm text-center mb-6', themeClasses.subtext]">This will permanently delete all your tasks. This action cannot be undone.</p>
          <div class="flex gap-3">
            <button 
              @click="showClearModal = false"
              :class="['flex-1 py-3 rounded-xl font-medium transition-all', themeClasses.buttonSecondary]"
            >
              Cancel
            </button>
            <button 
              @click="clearAllData"
              class="flex-1 py-3 rounded-xl bg-red-500 hover:bg-red-600 text-white font-medium transition-all"
            >
              Clear All
            </button>
          </div>
        </div>
      </div>
    </transition>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'

const props = defineProps({
  isDark: Boolean,
  themeClasses: Object,
  todos: Array,
  archivedTodos: Array
})

const emit = defineEmits(['toggle-theme', 'clear-data', 'import-data'])

const soundEnabled = ref(true)
const defaultView = ref('tasks')
const showClearModal = ref(false)

const views = [
  { value: 'tasks', label: 'Tasks', activeClass: 'bg-gradient-to-r from-purple-500 to-pink-500 text-white border-transparent' },
  { value: 'plan', label: 'Plan', activeClass: 'bg-gradient-to-r from-blue-500 to-cyan-500 text-white border-transparent' },
  { value: 'focus', label: 'Focus', activeClass: 'bg-gradient-to-r from-emerald-500 to-teal-500 text-white border-transparent' }
]

const toggleSound = () => {
  soundEnabled.value = !soundEnabled.value
  localStorage.setItem('soundEnabled', soundEnabled.value)
}

const setDefaultView = (view) => {
  defaultView.value = view
  localStorage.setItem('defaultView', view)
}

const exportData = () => {
  const data = {
    todos: props.todos,
    archivedTodos: props.archivedTodos,
    exportDate: new Date().toISOString()
  }
  const blob = new Blob([JSON.stringify(data, null, 2)], { type: 'application/json' })
  const url = URL.createObjectURL(blob)
  const a = document.createElement('a')
  a.href = url
  a.download = `todo-backup-${new Date().toISOString().split('T')[0]}.json`
  a.click()
  URL.revokeObjectURL(url)
}

const importData = (event) => {
  const file = event.target.files[0]
  if (!file) return
  
  const reader = new FileReader()
  reader.onload = (e) => {
    try {
      const data = JSON.parse(e.target.result)
      if (data.todos && Array.isArray(data.todos)) {
        emit('import-data', data)
        alert('Data imported successfully!')
      } else {
        alert('Invalid data format')
      }
    } catch (err) {
      alert('Failed to import data')
    }
  }
  reader.readAsText(file)
  event.target.value = ''
}

const confirmClearData = () => {
  showClearModal.value = true
}

const clearAllData = () => {
  emit('clear-data')
  showClearModal.value = false
}

onMounted(() => {
  const saved = localStorage.getItem('soundEnabled')
  if (saved !== null) soundEnabled.value = saved === 'true'
  
  const savedView = localStorage.getItem('defaultView')
  if (savedView) defaultView.value = savedView
})
</script>
