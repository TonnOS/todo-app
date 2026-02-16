<template>
  <div class="min-h-screen bg-slate-950 text-slate-100 font-sans selection:bg-purple-500/30 overflow-hidden">
    <!-- Animated Gradient Background -->
    <div class="fixed inset-0 bg-gradient-to-br from-slate-950 via-purple-950/30 to-pink-950/20 pointer-events-none"></div>
    <div class="fixed inset-0 bg-[radial-gradient(ellipse_at_top,_var(--tw-gradient-stops))] from-purple-900/20 via-transparent to-transparent pointer-events-none"></div>
    
    <!-- Animated Background Orbs -->
    <div class="fixed top-20 left-10 w-72 h-72 bg-purple-500/20 rounded-full blur-[120px] animate-pulse pointer-events-none"></div>
    <div class="fixed bottom-40 right-10 w-96 h-96 bg-pink-500/10 rounded-full blur-[100px] animate-pulse pointer-events-none" style="animation-delay: 1s;"></div>

    <!-- Hero Section -->
    <header class="relative pt-16 pb-8 px-6 z-10">
      <div class="flex justify-between items-start mb-6">
        <div class="flex-1">
          <div class="flex items-center gap-3 mb-2">
            <!-- Animated Sun/Moon -->
            <div class="relative w-12 h-12">
              <transition name="celestial" mode="out-in">
                <div v-if="isDaytime" key="sun" class="absolute inset-0">
                  <div class="w-12 h-12 rounded-full bg-gradient-to-br from-amber-300 to-orange-500 shadow-lg shadow-orange-500/50 animate-spin-slow">
                    <div class="absolute inset-0 rounded-full bg-gradient-to-br from-amber-200 to-orange-400 animate-pulse"></div>
                  </div>
                  <!-- Sun rays -->
                  <div class="absolute -inset-2 animate-spin-slow" style="animation-duration: 20s;">
                    <div v-for="i in 8" :key="i" class="absolute w-1 h-3 bg-amber-400/60 rounded-full" 
                         :style="{ 
                           top: '50%', 
                           left: '50%', 
                           transform: `translate(-50%, -50%) rotate(${i * 45}deg) translateY(-18px)` 
                         }"></div>
                  </div>
                </div>
                <div v-else key="moon" class="absolute inset-0">
                  <div class="w-12 h-12 rounded-full bg-gradient-to-br from-slate-200 to-slate-400 shadow-lg shadow-slate-400/30 relative overflow-hidden">
                    <div class="absolute top-1 right-2 w-3 h-3 rounded-full bg-slate-300/50"></div>
                    <div class="absolute bottom-3 left-3 w-2 h-2 rounded-full bg-slate-300/50"></div>
                    <div class="absolute top-4 left-5 w-1.5 h-1.5 rounded-full bg-slate-300/50"></div>
                  </div>
                </div>
              </transition>
            </div>
            <div>
              <p class="text-slate-400 text-sm font-medium">{{ currentDate }}</p>
              <h1 class="text-2xl font-bold bg-gradient-to-r from-white via-purple-200 to-pink-200 bg-clip-text text-transparent">
                {{ greeting }}
              </h1>
            </div>
          </div>
          
          <!-- Progress Stats -->
          <div class="flex items-center gap-4 mt-4">
            <div class="flex items-center gap-2 bg-white/5 backdrop-blur-sm rounded-full px-4 py-2 border border-white/10">
              <div class="w-2 h-2 rounded-full bg-emerald-400 animate-pulse"></div>
              <span class="text-sm text-slate-300">{{ doneCount }}/{{ todos.length }} done</span>
            </div>
            <div v-if="priorityTasks.length > 0" class="flex items-center gap-2 bg-red-500/10 backdrop-blur-sm rounded-full px-4 py-2 border border-red-500/20">
              <svg class="w-4 h-4 text-red-400" fill="currentColor" viewBox="0 0 24 24">
                <path d="M12 2L2 22h20L12 2zm0 3.5L18.5 20H5.5L12 5.5z"/>
                <path d="M11 10v6h2v-6h-2z"/>
              </svg>
              <span class="text-sm text-red-300">{{ priorityTasks.length }} priority</span>
            </div>
          </div>
        </div>

        <!-- Avatar -->
        <div class="w-12 h-12 rounded-2xl bg-gradient-to-tr from-purple-500 to-pink-500 p-[2px] shadow-lg shadow-purple-500/30">
          <div class="w-full h-full rounded-2xl bg-slate-900 flex items-center justify-center overflow-hidden">
            <span class="text-sm font-bold bg-gradient-to-r from-purple-400 to-pink-400 bg-clip-text text-transparent">OC</span>
          </div>
        </div>
      </div>

      <!-- Progress Bar -->
      <div class="relative h-2 w-full bg-white/5 rounded-full overflow-hidden">
        <div 
          class="h-full bg-gradient-to-r from-purple-500 via-pink-500 to-rose-500 transition-all duration-700 ease-out rounded-full"
          :style="{ width: progressWidth }"
        >
          <div class="absolute inset-0 bg-gradient-to-r from-transparent via-white/30 to-transparent animate-shimmer"></div>
        </div>
      </div>
    </header>

    <!-- Main Content -->
    <main class="relative px-5 pb-32 max-w-lg mx-auto min-h-[calc(100vh-200px)]">
      <!-- Todo List -->
      <transition-group 
        name="list" 
        tag="div" 
        class="space-y-3"
      >
        <div 
          v-for="(todo, index) in sortedTodos" 
          :key="todo.id"
          class="group relative"
          @touchstart="touchStart($event, index)"
          @touchmove="touchMove($event, index)"
          @touchend="touchEnd(index)"
        >
          <!-- Swipe Background -->
          <div 
            class="absolute inset-0 rounded-2xl flex items-center justify-end pr-5 transition-all duration-300"
            :class="swipedIndex === index ? 'bg-gradient-to-l from-red-500/20 to-transparent' : 'opacity-0'"
          >
            <div class="flex items-center gap-2 text-red-400">
              <span class="text-sm font-medium">Delete</span>
              <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16" />
              </svg>
            </div>
          </div>

          <!-- Card -->
          <div 
            class="relative bg-white/[0.08] backdrop-blur-xl rounded-2xl p-4 border border-white/10 shadow-xl transition-all duration-300 hover:bg-white/[0.12]"
            :class="[
              todo.done ? 'opacity-60' : '',
              swipedIndex === index ? 'translate-x-[-80px]' : 'translate-x-0'
            ]"
            :style="{ transform: swipedIndex === index ? 'translateX(-80px)' : getTransform(index) }"
          >
            <div class="flex items-center gap-3">
              <!-- Checkbox -->
              <button 
                @click="toggleTodo(todo)"
                class="relative w-7 h-7 rounded-full border-2 flex items-center justify-center transition-all duration-300 shrink-0"
                :class="todo.done ? 'bg-gradient-to-br from-emerald-400 to-emerald-600 border-transparent scale-110' : 'border-purple-400/50 hover:border-purple-400'"
              >
                <svg v-if="todo.done" class="w-4 h-4 text-white animate-bounce-in" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="3" d="M5 13l4 4L19 7" />
                </svg>
              </button>
              
              <!-- Content -->
              <div class="flex-1 min-w-0">
                <div class="flex items-center gap-2 flex-wrap">
                  <span 
                    class="text-base transition-all duration-300 truncate"
                    :class="todo.done ? 'line-through text-slate-500' : 'text-white'"
                  >
                    {{ todo.text }}
                  </span>
                </div>
                <div v-if="todo.dueTime" class="flex items-center gap-1 mt-1">
                  <svg class="w-3 h-3 text-slate-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8v4l3 3m6-3a9 9 0 11-18 0 9 9 0 0118 0z" />
                  </svg>
                  <span class="text-xs text-slate-500">{{ todo.dueTime }}</span>
                </div>
              </div>

              <!-- Priority Badge -->
              <div 
                v-if="todo.priority !== 'normal'"
                class="shrink-0 px-2.5 py-1 rounded-full text-[10px] font-bold uppercase tracking-wider"
                :class="{
                  'bg-gradient-to-r from-red-500/20 to-orange-500/20 text-red-300 border border-red-500/30': todo.priority === 'high',
                  'bg-gradient-to-r from-amber-500/20 to-yellow-500/20 text-amber-300 border border-amber-500/30': todo.priority === 'medium'
                }"
              >
                {{ todo.priority }}
              </div>

              <!-- Delete Button (Desktop) -->
              <button 
                @click="removeTodo(index)"
                class="opacity-0 group-hover:opacity-100 text-slate-500 hover:text-red-400 transition-all p-2 shrink-0"
              >
                <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16" />
                </svg>
              </button>
            </div>
          </div>
        </div>
      </transition-group>

      <!-- Floating Empty State -->
      <transition name="float">
        <div v-if="todos.length === 0" class="flex flex-col items-center justify-center py-12 text-center relative">
          <!-- Floating Animation Container -->
          <div class="relative">
            <!-- Floating Elements -->
            <div class="absolute -top-8 -left-12 w-6 h-6 rounded-full bg-purple-500/30 animate-float blur-sm" style="animation-delay: 0s;"></div>
            <div class="absolute -top-4 -right-8 w-4 h-4 rounded-full bg-pink-500/30 animate-float blur-sm" style="animation-delay: 0.5s;"></div>
            <div class="absolute top-16 -left-16 w-3 h-3 rounded-full bg-purple-400/20 animate-float blur-sm" style="animation-delay: 1s;"></div>
            <div class="absolute top-20 -right-12 w-5 h-5 rounded-full bg-pink-400/20 animate-float blur-sm" style="animation-delay: 1.5s;"></div>
            
            <!-- Main Icon -->
            <div class="w-28 h-28 rounded-3xl bg-gradient-to-br from-purple-500/20 to-pink-500/20 backdrop-blur-sm flex items-center justify-center mb-6 animate-float border border-white/10 shadow-2xl shadow-purple-500/10">
              <div class="relative">
                <svg class="w-14 h-14 text-purple-300" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2m-6 9l2 2 4-4" />
                </svg>
                <div class="absolute -top-1 -right-1 w-4 h-4 rounded-full bg-emerald-400 animate-pulse"></div>
              </div>
            </div>
          </div>
          
          <h3 class="text-xl font-bold text-white mb-2">All caught up!</h3>
          <p class="text-slate-400 text-sm max-w-[200px]">Your day is clear. Time to relax or add something new.</p>
          
          <button 
            @click="showModal = true"
            class="mt-6 px-6 py-3 rounded-full bg-gradient-to-r from-purple-500 to-pink-500 text-white font-medium text-sm shadow-lg shadow-purple-500/25 hover:shadow-purple-500/40 transition-all active:scale-95"
          >
            Add your first task
          </button>
        </div>
      </transition>
    </main>

    <!-- Pulsing FAB -->
    <div class="fixed bottom-24 right-6 z-40">
      <!-- Pulse Rings -->
      <div class="absolute inset-0 rounded-full bg-gradient-to-br from-purple-500 to-pink-500 animate-ping opacity-20"></div>
      <div class="absolute inset-[-4px] rounded-full bg-gradient-to-br from-purple-500 to-pink-500 animate-pulse opacity-30"></div>
      
      <button 
        @click="showModal = true"
        class="relative w-16 h-16 rounded-full bg-gradient-to-br from-purple-500 via-pink-500 to-rose-500 flex items-center justify-center shadow-2xl shadow-purple-500/50 hover:shadow-purple-500/70 hover:scale-105 active:scale-90 transition-all duration-300"
      >
        <svg class="w-8 h-8 text-white" fill="none" stroke="currentColor" viewBox="0 0 24 24">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2.5" d="M12 4v16m8-8H4" />
        </svg>
      </button>
    </div>

    <!-- Bottom Navigation with Active Indicator -->
    <nav class="fixed bottom-0 inset-x-0 h-[88px] bg-slate-950/80 backdrop-blur-2xl border-t border-white/5 flex items-center justify-around px-6 pb-6 z-30">
      <!-- Active Indicator Pill -->
      <div 
        class="absolute bottom-6 h-10 rounded-full bg-gradient-to-r from-purple-500/20 to-pink-500/20 border border-white/10 transition-all duration-500 ease-out"
        :style="{ 
          width: '64px', 
          left: `${activeNavPosition}px`,
          transform: 'translateX(-50%)'
        }"
      ></div>

      <button 
        v-for="(item, index) in navItems" 
        :key="item.name"
        @click="activeNav = index"
        :ref="el => { if(el) navRefs[index] = el }"
        class="relative flex flex-col items-center gap-1.5 py-2 px-4 transition-all duration-300 z-10"
        :class="activeNav === index ? 'text-white scale-110' : 'text-slate-500 hover:text-slate-300'"
      >
        <svg 
          class="w-6 h-6 transition-transform duration-300" 
          :class="activeNav === index ? 'scale-110' : ''"
          :fill="item.fill || 'none'" 
          :stroke="item.fill ? 'none' : 'currentColor'"
          viewBox="0 0 24 24"
        >
          <path v-if="item.path" :stroke-linecap="item.strokeLinecap || 'round'" :stroke-linejoin="item.strokeLinejoin || 'round'" :stroke-width="item.strokeWidth || '2'" :d="item.path" :fill="item.fill || 'none'" />
          <path v-if="item.path2" :stroke-linecap="item.strokeLinecap || 'round'" :stroke-linejoin="item.strokeLinejoin || 'round'" :stroke-width="item.strokeWidth || '2'" :d="item.path2" />
        </svg>
        <span class="text-[10px] font-semibold uppercase tracking-tight">{{ item.name }}</span>
      </button>
    </nav>

    <!-- Add Task Modal -->
    <transition name="modal">
      <div v-if="showModal" class="fixed inset-0 z-50 flex items-end justify-center bg-slate-950/60 backdrop-blur-sm" @click="showModal = false">
        <div class="w-full max-w-lg bg-slate-900 rounded-t-[32px] p-6 border-t border-white/10 shadow-2xl transition-all" @click.stop>
          <!-- Handle -->
          <div class="w-10 h-1 bg-white/20 rounded-full mx-auto mb-6"></div>
          
          <h2 class="text-xl font-bold text-white mb-5">New Task</h2>
          
          <!-- Task Input -->
          <div class="relative mb-4">
            <input 
              v-model="newTodo"
              @keyup.enter="addTodo"
              type="text" 
              placeholder="What needs to be done?" 
              class="w-full bg-white/5 border border-white/10 rounded-2xl px-5 py-4 text-white placeholder-slate-500 focus:outline-none focus:ring-2 focus:ring-purple-500/50 focus:border-purple-500/30 transition-all text-base"
              ref="taskInput"
            />
          </div>

          <!-- Priority Selection -->
          <div class="flex gap-2 mb-5">
            <button 
              v-for="p in priorities" 
              :key="p.value"
              @click="selectedPriority = p.value"
              class="flex-1 py-3 rounded-xl text-sm font-medium transition-all border"
              :class="selectedPriority === p.value ? p.activeClass : 'bg-white/5 border-white/10 text-slate-400 hover:bg-white/10'"
            >
              {{ p.label }}
            </button>
          </div>

          <!-- Add Button -->
          <button 
            @click="addTodo"
            :disabled="!newTodo.trim()"
            class="w-full py-4 rounded-2xl bg-gradient-to-r from-purple-500 to-pink-500 text-white font-bold text-base shadow-lg shadow-purple-500/20 active:scale-[0.98] transition-all disabled:opacity-50 disabled:cursor-not-allowed"
          >
            Create Task
          </button>
        </div>
      </div>
    </transition>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, nextTick, watch } from 'vue'
import confetti from 'canvas-confetti'

// Data
const todos = ref([
  { id: 1, text: 'Review project proposal', done: false, priority: 'high', dueTime: '2:00 PM' },
  { id: 2, text: 'Team standup meeting', done: true, priority: 'normal', dueTime: '10:00 AM' },
  { id: 3, text: 'Update documentation', done: false, priority: 'medium', dueTime: '4:00 PM' },
  { id: 4, text: 'Code review', done: false, priority: 'high', dueTime: '5:30 PM' }
])

const newTodo = ref('')
const showModal = ref(false)
const selectedPriority = ref('normal')
const taskInput = ref(null)
const activeNav = ref(0)
const swipedIndex = ref(null)
const touchStartX = ref(0)
const touchCurrentX = ref(0)
const navRefs = ref([])

// Priorities
const priorities = [
  { value: 'normal', label: 'Normal', activeClass: 'bg-slate-600 border-slate-500 text-white' },
  { value: 'medium', label: 'Medium', activeClass: 'bg-gradient-to-r from-amber-500 to-yellow-500 border-amber-400 text-white' },
  { value: 'high', label: 'High', activeClass: 'bg-gradient-to-r from-red-500 to-orange-500 border-red-400 text-white' }
]

// Navigation items
const navItems = [
  { 
    name: 'Tasks', 
    path: 'M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2m-6 9l2 2 4-4',
    fill: 'currentColor'
  },
  { 
    name: 'Plan', 
    path: 'M8 7V3m8 4V3m-9 8h10M5 21h14a2 2 0 002-2V7a2 2 0 00-2-2H5a2 2 0 00-2 2v12a2 2 0 002 2z',
    strokeLinecap: 'round',
    strokeLinejoin: 'round'
  },
  { 
    name: 'Focus', 
    path: 'M12 8v4l3 3m6-3a9 9 0 11-18 0 9 9 0 0118 0z',
    strokeLinecap: 'round',
    strokeLinejoin: 'round'
  },
  { 
    name: 'Settings', 
    path: 'M10.325 4.317c.426-1.756 2.924-1.756 3.35 0a1.724 1.724 0 002.573 1.066c1.543-.94 3.31.826 2.37 2.37a1.724 1.724 0 001.065 2.572c1.756.426 1.756 2.924 0 3.35a1.724 1.724 0 00-1.066 2.573c.94 1.543-.826 3.31-2.37 2.37a1.724 1.724 0 00-2.572 1.065c-.426 1.756-2.924 1.756-3.35 0a1.724 1.724 0 00-2.573-1.066c-1.543.94-3.31-.826-2.37-2.37a1.724 1.724 0 00-1.065-2.572c-1.756-.426-1.756-2.924 0-3.35a1.724 1.724 0 001.066-2.573c-.94-1.543.826-3.31 2.37-2.37.996.608 2.296.07 2.572-1.065z',
    path2: 'M15 12a3 3 0 11-6 0 3 3 0 016 0z',
    strokeLinecap: 'round',
    strokeLinejoin: 'round'
  }
]

// Computed
const currentDate = computed(() => {
  return new Date().toLocaleDateString('en-US', { 
    weekday: 'short', 
    month: 'short', 
    day: 'numeric' 
  })
})

const hour = computed(() => new Date().getHours())

const isDaytime = computed(() => hour.value >= 6 && hour.value < 18)

const greeting = computed(() => {
  const h = hour.value
  if (h < 12) return 'Good morning'
  if (h < 17) return 'Good afternoon'
  return 'Good evening'
})

const doneCount = computed(() => todos.value.filter(t => t.done).length)
const progressWidth = computed(() => {
  if (todos.value.length === 0) return '0%'
  return `${(doneCount.value / todos.value.length) * 100}%`
})

const priorityTasks = computed(() => todos.value.filter(t => t.priority === 'high' && !t.done))

const sortedTodos = computed(() => {
  return [...todos.value].sort((a, b) => {
    if (a.done !== b.done) return a.done ? 1 : -1
    const priorityOrder = { high: 0, medium: 1, normal: 2 }
    return priorityOrder[a.priority] - priorityOrder[b.priority]
  })
})

const activeNavPosition = computed(() => {
  const el = navRefs.value[activeNav.value]
  return el ? el.offsetLeft + el.offsetWidth / 2 : 0
})

// Methods
const toggleTodo = (todo) => {
  const wasDone = todo.done
  todo.done = !wasDone
  
  if (!wasDone && todo.done) {
    triggerConfetti()
  }
}

const triggerConfetti = () => {
  const count = 200
  const defaults = {
    origin: { y: 0.7 },
    colors: ['#a855f7', '#ec4899', '#f43f5e', '#10b981', '#fbbf24']
  }

  confetti({
    ...defaults,
    particleCount: count,
    spread: 100,
    decay: 0.9,
    scalar: 1.2
  })

  setTimeout(() => {
    confetti({
      ...defaults,
      particleCount: count / 2,
      spread: 60,
      decay: 0.9,
      scalar: 0.8
    })
  }, 150)
}

const addTodo = () => {
  if (newTodo.value.trim()) {
    todos.value.push({
      id: Date.now(),
      text: newTodo.value.trim(),
      done: false,
      priority: selectedPriority.value,
      dueTime: new Date().toLocaleTimeString('en-US', { hour: 'numeric', minute: '2-digit' })
    })
    newTodo.value = ''
    selectedPriority.value = 'normal'
    showModal.value = false
  }
}

const removeTodo = (index) => {
  todos.value.splice(index, 1)
  swipedIndex.value = null
}

// Swipe handlers
const touchStart = (e, index) => {
  touchStartX.value = e.touches[0].clientX
  touchCurrentX.value = touchStartX.value
}

const touchMove = (e, index) => {
  touchCurrentX.value = e.touches[0].clientX
  const diff = touchStartX.value - touchCurrentX.value
  if (diff > 50) {
    swipedIndex.value = index
  } else if (diff < -50) {
    swipedIndex.value = null
  }
}

const touchEnd = (index) => {
  const diff = touchStartX.value - touchCurrentX.value
  if (diff > 100) {
    setTimeout(() => removeTodo(index), 200)
  } else if (diff < 50) {
    swipedIndex.value = null
  }
}

const getTransform = (index) => {
  return 'translateX(0)'
}

// Watch modal
watch(showModal, (val) => {
  if (val) {
    nextTick(() => {
      taskInput.value?.focus()
    })
  }
})

onMounted(() => {
  nextTick(() => {
    // Initial nav position calculation
  })
})
</script>

<style>
/* List transitions */
.list-enter-active, .list-leave-active { 
  transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1); 
}
.list-enter-from { 
  opacity: 0; 
  transform: translateY(20px) scale(0.95); 
}
.list-leave-to { 
  opacity: 0; 
  transform: translateX(-100%); 
}

/* Modal transitions */
.modal-enter-active, .modal-leave-active { 
  transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1); 
}
.modal-enter-from, .modal-leave-to { 
  opacity: 0;
}
.modal-enter-from > div:last-child,
.modal-leave-to > div:last-child { 
  transform: translateY(100%); 
}

/* Celestial transitions */
.celestial-enter-active, .celestial-leave-active {
  transition: all 0.6s cubic-bezier(0.4, 0, 0.2, 1);
}
.celestial-enter-from {
  opacity: 0;
  transform: scale(0.5) rotate(-180deg);
}
.celestial-leave-to {
  opacity: 0;
  transform: scale(0.5) rotate(180deg);
}

/* Float transition */
.float-enter-active, .float-leave-active {
  transition: all 0.6s cubic-bezier(0.4, 0, 0.2, 1);
}
.float-enter-from, .float-leave-to {
  opacity: 0;
  transform: translateY(30px) scale(0.9);
}

/* Animations */
@keyframes shimmer {
  0% { transform: translateX(-100%); }
  100% { transform: translateX(100%); }
}

@keyframes spin-slow {
  from { transform: rotate(0deg); }
  to { transform: rotate(360deg); }
}

@keyframes float {
  0%, 100% { transform: translateY(0px) rotate(0deg); }
  50% { transform: translateY(-15px) rotate(2deg); }
}

@keyframes bounce-in {
  0% { transform: scale(0); }
  50% { transform: scale(1.2); }
  100% { transform: scale(1); }
}

.animate-shimmer {
  animation: shimmer 2s infinite;
}

.animate-spin-slow {
  animation: spin-slow 10s linear infinite;
}

.animate-float {
  animation: float 3s ease-in-out infinite;
}

.animate-bounce-in {
  animation: bounce-in 0.3s cubic-bezier(0.68, -0.55, 0.265, 1.55);
}

/* Smooth scrolling */
html {
  scroll-behavior: smooth;
}

/* Custom scrollbar */
::-webkit-scrollbar {
  width: 0px;
}
</style>
