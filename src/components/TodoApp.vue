<template>
  <div class="min-h-screen bg-slate-900 text-slate-100 font-sans selection:bg-purple-500/30">
    <!-- Gradient Background -->
    <div class="fixed inset-0 bg-gradient-to-br from-slate-950 via-slate-900 to-purple-900/20 pointer-events-none"></div>
    
    <!-- Header -->
    <header class="relative pt-12 pb-6 px-6 backdrop-blur-md bg-slate-900/50 border-b border-white/5 sticky top-0 z-30">
      <div class="flex justify-between items-center mb-2">
        <h1 class="text-3xl font-black tracking-tight bg-gradient-to-r from-white to-slate-400 bg-clip-text text-transparent">
          My Day
        </h1>
        <div class="w-10 h-10 rounded-full bg-gradient-to-tr from-purple-500 to-pink-500 p-[2px]">
          <div class="w-full h-full rounded-full bg-slate-900 flex items-center justify-center overflow-hidden">
            <span class="text-xs font-bold">OC</span>
          </div>
        </div>
      </div>
      <p class="text-slate-400 text-sm font-medium uppercase tracking-widest">{{ currentDate }}</p>
      
      <!-- Progress Bar -->
      <div class="mt-6 h-1 w-full bg-white/5 rounded-full overflow-hidden">
        <div 
          class="h-full bg-gradient-to-r from-purple-500 to-pink-500 transition-all duration-500"
          :style="{ width: progressWidth }"
        ></div>
      </div>
    </header>

    <!-- Main Content -->
    <main class="relative px-6 py-8 pb-32 max-w-lg mx-auto min-h-screen">
      <transition-group 
        name="list" 
        tag="div" 
        class="space-y-4"
      >
        <div 
          v-for="(todo, index) in todos" 
          :key="todo.id"
          class="group relative bg-white/10 backdrop-blur-lg rounded-2xl p-4 border border-white/10 shadow-xl transition-all duration-300 hover:bg-white/15 active:scale-[0.98]"
        >
          <div class="flex items-center gap-4">
            <button 
              @click="toggleTodo(index)"
              class="w-7 h-7 rounded-full border-2 flex items-center justify-center transition-all duration-300"
              :class="todo.done ? 'bg-gradient-to-br from-green-400 to-emerald-500 border-transparent' : 'border-purple-400 hover:border-purple-300'"
            >
              <svg v-if="todo.done" class="w-4 h-4 text-white" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="3" d="M5 13l4 4L19 7" />
              </svg>
            </button>
            
            <span 
              class="flex-1 text-lg transition-all duration-300"
              :class="todo.done ? 'line-through text-slate-500' : 'text-white'"
            >
              {{ todo.text }}
            </span>

            <button 
              @click="removeTodo(index)"
              class="opacity-0 group-hover:opacity-100 text-slate-500 hover:text-red-400 transition-all p-1"
            >
              <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16" />
              </svg>
            </button>
          </div>
        </div>
      </transition-group>

      <!-- Empty State -->
      <div v-if="todos.length === 0" class="flex flex-col items-center justify-center py-20 text-center">
        <div class="w-20 h-20 rounded-3xl bg-white/5 flex items-center justify-center mb-6 rotate-12 animation-pulse">
          <svg class="w-10 h-10 text-purple-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2m-6 9l2 2 4-4" />
          </svg>
        </div>
        <h3 class="text-xl font-bold text-white mb-2">Clear as crystal</h3>
        <p class="text-slate-400">Enjoy your free time or add a new task below.</p>
      </div>
    </main>

    <!-- FAB -->
    <button 
      @click="showModal = true"
      class="fixed bottom-24 right-6 w-16 h-16 rounded-full bg-gradient-to-br from-purple-500 to-pink-500 flex items-center justify-center shadow-2xl shadow-purple-500/40 hover:shadow-purple-500/60 transition-all active:scale-[0.9] z-40"
    >
      <svg class="w-8 h-8 text-white" fill="none" stroke="currentColor" viewBox="0 0 24 24">
        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 4v16m8-8H4" />
      </svg>
    </button>

    <!-- Navigation -->
    <nav class="fixed bottom-0 inset-x-0 h-20 bg-slate-900/80 backdrop-blur-xl border-t border-white/5 flex items-center justify-around px-6 z-30">
      <button class="flex flex-col items-center gap-1 text-purple-400">
        <svg class="w-6 h-6" fill="currentColor" viewBox="0 0 24 24"><path d="M3 12l2-2m0 0l7-7 7 7M5 10v10a1 1 0 001 1h3m10-11l2 2m-2-2v10a1 1 0 01-1 1h-3m-6 0a1 1 0 001-1v-4a1 1 0 011-1h2a1 1 0 011 1v4a1 1 0 001 1m-6 0h6" /></svg>
        <span class="text-[10px] font-bold uppercase tracking-tighter">Tasks</span>
      </button>
      <button class="flex flex-col items-center gap-1 text-slate-500">
        <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 7V3m8 4V3m-9 8h10M5 21h14a2 2 0 002-2V7a2 2 0 00-2-2H5a2 2 0 00-2 2v12a2 2 0 002 2z" /></svg>
        <span class="text-[10px] font-bold uppercase tracking-tighter">Plan</span>
      </button>
      <div class="w-16"></div>
      <button class="flex flex-col items-center gap-1 text-slate-500">
        <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 19v-6a2 2 0 00-2-2H5a2 2 0 00-2 2v6a2 2 0 002 2h2a2 2 0 002-2zm0 0V9a2 2 0 012-2h2a2 2 0 012 2v10m-6 0a2 2 0 002 2h2a2 2 0 002-2m0 0V5a2 2 0 012-2h2a2 2 0 012 2v14a2 2 0 01-2 2h-2a2 2 0 01-2-2z" /></svg>
        <span class="text-[10px] font-bold uppercase tracking-tighter">Stats</span>
      </button>
      <button class="flex flex-col items-center gap-1 text-slate-500">
        <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10.325 4.317c.426-1.756 2.924-1.756 3.35 0a1.724 1.724 0 002.573 1.066c1.543-.94 3.31.826 2.37 2.37a1.724 1.724 0 001.065 2.572c1.756.426 1.756 2.924 0 3.35a1.724 1.724 0 00-1.066 2.573c.94 1.543-.826 3.31-2.37 2.37a1.724 1.724 0 00-2.572 1.065c-.426 1.756-2.924 1.756-3.35 0a1.724 1.724 0 00-2.573-1.066c-1.543.94-3.31-.826-2.37-2.37a1.724 1.724 0 00-1.065-2.572c-1.756-.426-1.756-2.924 0-3.35a1.724 1.724 0 001.066-2.573c-.94-1.543.826-3.31 2.37-2.37.996.608 2.296.07 2.572-1.065z" /><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z" /></svg>
        <span class="text-[10px] font-bold uppercase tracking-tighter">Settings</span>
      </button>
    </nav>

    <!-- Modal -->
    <transition name="modal">
      <div v-if="showModal" class="fixed inset-0 z-50 flex items-end justify-center bg-slate-950/60 backdrop-blur-sm" @click="showModal = false">
        <div class="w-full max-w-lg bg-slate-900 rounded-t-[40px] p-8 border-t border-white/10 shadow-2xl transition-all" @click.stop>
          <div class="w-12 h-1.5 bg-white/10 rounded-full mx-auto mb-8"></div>
          <h2 class="text-2xl font-bold text-white mb-6">New Task</h2>
          <div class="relative">
            <input 
              v-model="newTodo"
              @keyup.enter="addTodo"
              type="text" 
              placeholder="What's your focus?" 
              class="w-full bg-white/5 border border-white/10 rounded-2xl px-6 py-4 text-white placeholder-slate-500 focus:outline-none focus:ring-2 focus:ring-purple-500/50 transition-all text-lg"
              autoFocus
            />
          </div>
          <button 
            @click="addTodo"
            class="w-full mt-6 py-4 rounded-2xl bg-gradient-to-r from-purple-500 to-pink-500 text-white font-bold text-lg shadow-lg shadow-purple-500/20 active:scale-95 transition-transform"
          >
            Create Task
          </button>
        </div>
      </div>
    </transition>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'

const todos = ref([
  { id: 1, text: 'Brainstorm design concepts', done: true },
  { id: 2, text: 'Implement glassmorphism', done: false },
  { id: 3, text: 'Push to production', done: false }
])
const newTodo = ref('')
const showModal = ref(false)

const currentDate = computed(() => {
  return new Date().toLocaleDateString('en-US', { 
    weekday: 'long', 
    month: 'long', 
    day: 'numeric' 
  })
})

const doneCount = computed(() => todos.value.filter(t => t.done).length)
const progressWidth = computed(() => {
  if (todos.value.length === 0) return '0%'
  return `${(doneCount.value / todos.value.length) * 100}%`
})

const toggleTodo = (index) => {
  todos.value[index].done = !todos.value[index].done
}

const addTodo = () => {
  if (newTodo.value.trim()) {
    todos.value.push({
      id: Date.now(),
      text: newTodo.value.trim(),
      done: false
    })
    newTodo.value = ''
    showModal.value = false
  }
}

const removeTodo = (index) => {
  todos.value.splice(index, 1)
}
</script>

<style>
.list-enter-active, .list-leave-active { transition: all 0.4s ease; }
.list-enter-from { opacity: 0; transform: translateX(-30px); }
.list-leave-to { opacity: 0; transform: translateX(30px); }

.modal-enter-active, .modal-leave-active { transition: transform 0.4s cubic-bezier(0.4, 0, 0.2, 1), opacity 0.3s linear; }
.modal-enter-from, .modal-leave-to { transform: translateY(100%); opacity: 0; }
</style>
