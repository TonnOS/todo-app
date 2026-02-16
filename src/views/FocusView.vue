<template>
  <div class="space-y-4">
    <!-- Timer Display -->
    <div :class="['backdrop-blur-xl rounded-2xl p-6 border shadow-lg flex flex-col items-center', themeClasses.card]">
      <!-- Mode Selector -->
      <div class="flex items-center gap-2 mb-6">
        <button
          v-for="mode in timerModes"
          :key="mode.value"
          @click="switchMode(mode.value)"
          :class="[
            'px-4 py-2 rounded-full text-xs font-medium transition-all duration-300 border',
            currentMode === mode.value
              ? mode.activeClass
              : themeClasses.modeInactive
          ]"
        >
          {{ mode.label }}
        </button>
      </div>

      <!-- Progress Ring Timer -->
      <div class="relative mb-6">
        <!-- Outer glow -->
        <div
          class="absolute inset-0 rounded-full blur-xl opacity-50"
          :class="isRunning ? 'bg-gradient-to-br from-purple-500 to-pink-500 animate-pulse' : ''"
          style="transform: scale(1.1);"
        ></div>

        <!-- SVG Ring -->
        <svg class="w-64 h-64 transform -rotate-90 relative z-10">
          <!-- Background circle -->
          <circle
            cx="128"
            cy="128"
            r="120"
            fill="none"
            :stroke="isDark ? 'rgba(255,255,255,0.1)' : 'rgba(0,0,0,0.1)'"
            stroke-width="8"
          />
          <!-- Progress circle -->
          <circle
            cx="128"
            cy="128"
            r="120"
            fill="none"
            :stroke="currentModeData.color"
            stroke-width="8"
            stroke-linecap="round"
            :stroke-dasharray="circumference"
            :stroke-dashoffset="strokeDashoffset"
            class="transition-all duration-1000 ease-linear"
          />
        </svg>

        <!-- Time Display -->
        <div class="absolute inset-0 flex flex-col items-center justify-center z-20">
          <span
            :class="[
              'text-6xl font-bold tabular-nums tracking-tight',
              isRunning ? 'text-white' : themeClasses.timerText
            ]"
            :style="{ textShadow: isRunning ? `0 0 30px ${currentModeData.color}` : 'none' }"
          >
            {{ formattedTime }}
          </span>
          <span :class="['text-sm mt-2', themeClasses.subtext]">
            {{ isRunning ? (isPaused ? 'Paused' : 'Focusing...') : 'Ready to focus' }}
          </span>
        </div>
      </div>

      <!-- Control Buttons -->
      <div class="flex items-center gap-4">
        <button
          @click="resetTimer"
          :class="[
            'w-12 h-12 rounded-full flex items-center justify-center transition-all duration-300 border',
            themeClasses.resetBtn
          ]"
        >
          <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15" />
          </svg>
        </button>

        <button
          @click="toggleTimer"
          :class="[
            'w-20 h-20 rounded-full flex items-center justify-center transition-all duration-300 shadow-2xl',
            isRunning && !isPaused
              ? 'bg-gradient-to-br from-amber-400 to-orange-500 shadow-orange-500/30 hover:shadow-orange-500/50'
              : 'bg-gradient-to-br from-purple-500 to-pink-500 shadow-purple-500/30 hover:shadow-purple-500/50'
          ]"
        >
          <svg
            v-if="!isRunning || isPaused"
            class="w-10 h-10 text-white ml-1"
            fill="currentColor"
            viewBox="0 0 24 24"
          >
            <path d="M8 5v14l11-7z" />
          </svg>
          <svg
            v-else
            class="w-10 h-10 text-white"
            fill="currentColor"
            viewBox="0 0 24 24"
          >
            <path d="M6 4h4v16H6V4zm8 0h4v16h-4V4z" />
          </svg>
        </button>

        <button
          @click="showSettings = !showSettings"
          :class="[
            'w-12 h-12 rounded-full flex items-center justify-center transition-all duration-300 border',
            showSettings ? 'bg-purple-500/20 border-purple-500/30 text-purple-400' : themeClasses.settingsBtn
          ]"
        >
          <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10.325 4.317c.426-1.756 2.924-1.756 3.35 0a1.724 1.724 0 002.573 1.066c1.543-.94 3.31.826 2.37 2.37a1.724 1.724 0 001.065 2.572c1.756.426 1.756 2.924 0 3.35a1.724 1.724 0 00-1.066 2.573c.94 1.543-.826 3.31-2.37 2.37a1.724 1.724 0 00-2.572 1.065c-.426 1.756-2.924 1.756-3.35 0a1.724 1.724 0 00-2.573-1.066c-1.543.94-3.31-.826-2.37-2.37a1.724 1.724 0 00-1.065-2.572c-1.756-.426-1.756-2.924 0-3.35a1.724 1.724 0 001.066-2.573c-.94-1.543.826-3.31 2.37-2.37.996.608 2.296.07 2.572-1.065z" />
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z" />
          </svg>
        </button>
      </div>
    </div>

    <!-- Timer Settings -->
    <transition name="slide-up">
      <div
        v-if="showSettings"
        :class="['backdrop-blur-xl rounded-2xl p-4 border shadow-lg', themeClasses.card]"
      >
        <div class="flex items-center justify-between mb-4">
          <h3 :class="['text-sm font-semibold', themeClasses.heading]">Timer Settings</h3>
          <button
            @click="showSettings = false"
            :class="['p-1 rounded-lg transition-colors', themeClasses.closeBtn]"
          >
            <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
            </svg>
          </button>
        </div>

        <div class="space-y-4">
          <!-- Focus Duration -->
          <div>
            <div class="flex items-center justify-between mb-2">
              <label :class="['text-xs font-medium', themeClasses.label]">Focus Duration</label>
              <span :class="['text-xs font-bold', themeClasses.value]">{{ settings.focus }} min</span>
            </div>
            <input
              v-model.number="settings.focus"
              type="range"
              min="5"
              max="60"
              step="5"
              :class="['w-full h-2 rounded-lg appearance-none cursor-pointer', themeClasses.slider]"
              @change="updateSettings"
            >
          </div>

          <!-- Short Break -->
          <div>
            <div class="flex items-center justify-between mb-2">
              <label :class="['text-xs font-medium', themeClasses.label]">Short Break</label>
              <span :class="['text-xs font-bold', themeClasses.value]">{{ settings.shortBreak }} min</span>
            </div>
            <input
              v-model.number="settings.shortBreak"
              type="range"
              min="1"
              max="15"
              step="1"
              :class="['w-full h-2 rounded-lg appearance-none cursor-pointer', themeClasses.slider]"
              @change="updateSettings"
            >
          </div>

          <!-- Long Break -->
          <div>
            <div class="flex items-center justify-between mb-2">
              <label :class="['text-xs font-medium', themeClasses.label]">Long Break</label>
              <span :class="['text-xs font-bold', themeClasses.value]">{{ settings.longBreak }} min</span>
            </div>
            <input
              v-model.number="settings.longBreak"
              type="range"
              min="5"
              max="30"
              step="5"
              :class="['w-full h-2 rounded-lg appearance-none cursor-pointer', themeClasses.slider]"
              @change="updateSettings"
            >
          </div>

          <!-- Auto-start Breaks -->
          <div class="flex items-center justify-between py-2">
            <label :class="['text-xs font-medium', themeClasses.label]">Auto-start breaks</label>
            <button
              @click="settings.autoStartBreaks = !settings.autoStartBreaks; updateSettings()"
              :class="[
                'w-11 h-6 rounded-full transition-colors relative',
                settings.autoStartBreaks ? 'bg-purple-500' : themeClasses.toggleBg
              ]"
            >
              <span
                :class="[
                  'absolute top-1 left-1 w-4 h-4 rounded-full bg-white transition-transform duration-300',
                  settings.autoStartBreaks ? 'translate-x-5' : 'translate-x-0'
                ]"
              ></span>
            </button>
          </div>
        </div>
      </div>
    </transition>

    <!-- Session Stats -->
    <div :class="['backdrop-blur-xl rounded-2xl p-4 border shadow-lg', themeClasses.card]">
      <h3 :class="['text-sm font-semibold mb-3', themeClasses.heading]">Today's Focus</h3>
      <div class="grid grid-cols-3 gap-3">
        <div
          v-for="stat in focusStats"
          :key="stat.label"
          :class="['text-center p-3 rounded-xl', themeClasses.statCard]"
        >
          <div class="flex items-center justify-center gap-1 mb-1">
            <span class="text-lg">{{ stat.icon }}</span>
            <span :class="['text-xl font-bold', stat.colorClass]">{{ stat.value }}</span>
          </div>
          <p :class="['text-[10px] uppercase tracking-wide', themeClasses.subtext]">{{ stat.label }}</p>
        </div>
      </div>
    </div>

    <!-- Task Selection for Focus -->
    <div :class="['backdrop-blur-xl rounded-2xl p-4 border shadow-lg', themeClasses.card]">
      <h3 :class="['text-sm font-semibold mb-3', themeClasses.heading]">Current Focus Task</h3>
      <div v-if="incompleteTodos.length > 0" class="space-y-2">
        <button
          v-for="todo in incompleteTodos.slice(0, 3)"
          :key="todo.id"
          @click="selectedTask = todo.id"
          :class="[
            'w-full flex items-center gap-3 p-3 rounded-xl border transition-all duration-300 text-left',
            selectedTask === todo.id
              ? 'bg-purple-500/20 border-purple-500/30'
              : themeClasses.taskItem
          ]"
        >
          <div
            :class="[
              'w-5 h-5 rounded-full border-2 flex items-center justify-center shrink-0',
              selectedTask === todo.id ? 'border-purple-400 bg-purple-400' : 'border-slate-500'
            ]"
          >
            <svg
              v-if="selectedTask === todo.id"
              class="w-3 h-3 text-white"
              fill="none"
              stroke="currentColor"
              viewBox="0 0 24 24"
            >
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="3" d="M5 13l4 4L19 7" />
            </svg>
          </div>
          <div class="flex-1 min-w-0">
            <p :class="['text-sm truncate', themeClasses.todoText]">{{ todo.text }}</p>
            <div class="flex items-center gap-2 mt-1">
              <span
                v-if="todo.category"
                class="text-[10px] px-1.5 py-0.5 rounded-full"
                :class="getCategoryClass(todo.category)"
              >
                {{ getCategoryIcon(todo.category) }} {{ todo.category }}
              </span>
              <span
                v-if="todo.priority !== 'normal'"
                class="text-[10px] px-1.5 py-0.5 rounded-full uppercase"
                :class="getPriorityClass(todo.priority)"
              >
                {{ todo.priority }}
              </span>
            </div>
          </div>
        </button>
      </div>
      <div v-else class="text-center py-6">
        <p :class="['text-sm', themeClasses.subtext]">No tasks to focus on</p>
        <p :class="['text-xs mt-1', themeClasses.subtext]">Add tasks from the Tasks view</p>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, onUnmounted, watch } from 'vue'

const props = defineProps({
  todos: {
    type: Array,
    required: true
  },
  isDark: {
    type: Boolean,
    required: true
  }
})

const emit = defineEmits(['complete-task'])

// Timer state
const currentMode = ref('focus')
const isRunning = ref(false)
const isPaused = ref(false)
const timeLeft = ref(25 * 60)
const showSettings = ref(false)
const selectedTask = ref(null)
const timerInterval = ref(null)
const completedSessions = ref(0)

// Default settings
const settings = ref({
  focus: 25,
  shortBreak: 5,
  longBreak: 15,
  autoStartBreaks: false
})

// Load settings from localStorage
onMounted(() => {
  const savedSettings = localStorage.getItem('focusSettings')
  if (savedSettings) {
    settings.value = JSON.parse(savedSettings)
    timeLeft.value = settings.value.focus * 60
  }
  const savedSessions = localStorage.getItem('completedSessions')
  if (savedSessions) {
    completedSessions.value = parseInt(savedSessions)
  }
})

onUnmounted(() => {
  if (timerInterval.value) {
    clearInterval(timerInterval.value)
  }
})

const timerModes = computed(() => [
  {
    value: 'focus',
    label: 'Focus',
    activeClass: 'bg-gradient-to-r from-purple-500 to-pink-500 border-purple-400 text-white',
    color: '#a855f7',
    duration: settings.value.focus
  },
  {
    value: 'shortBreak',
    label: 'Short Break',
    activeClass: 'bg-gradient-to-r from-emerald-500 to-teal-500 border-emerald-400 text-white',
    color: '#10b981',
    duration: settings.value.shortBreak
  },
  {
    value: 'longBreak',
    label: 'Long Break',
    activeClass: 'bg-gradient-to-r from-blue-500 to-cyan-500 border-blue-400 text-white',
    color: '#3b82f6',
    duration: settings.value.longBreak
  }
])

const currentModeData = computed(() => {
  return timerModes.value.find(m => m.value === currentMode.value) || timerModes.value[0]
})

const circumference = 2 * Math.PI * 120

const strokeDashoffset = computed(() => {
  const totalSeconds = currentModeData.value.duration * 60
  const progress = (totalSeconds - timeLeft.value) / totalSeconds
  return circumference * progress
})

const formattedTime = computed(() => {
  const minutes = Math.floor(timeLeft.value / 60)
  const seconds = timeLeft.value % 60
  return `${minutes.toString().padStart(2, '0')}:${seconds.toString().padStart(2, '0')}`
})

const incompleteTodos = computed(() => {
  return props.todos.filter(t => !t.done)
})

const focusStats = computed(() => [
  {
    label: 'Sessions',
    value: completedSessions.value,
    icon: '🍅',
    colorClass: props.isDark ? 'text-purple-400' : 'text-purple-600'
  },
  {
    label: 'Focus Time',
    value: `${Math.floor((completedSessions.value * settings.value.focus) / 60)}h`,
    icon: '⏱️',
    colorClass: props.isDark ? 'text-pink-400' : 'text-pink-600'
  },
  {
    label: 'Streak',
    value: '3',
    icon: '🔥',
    colorClass: 'text-orange-400'
  }
])

const themeClasses = computed(() => ({
  card: props.isDark
    ? 'bg-white/[0.08] border-white/10'
    : 'bg-white/80 border-slate-200/50',
  heading: props.isDark ? 'text-white' : 'text-slate-800',
  subtext: props.isDark ? 'text-slate-400' : 'text-slate-500',
  timerText: props.isDark ? 'text-white' : 'text-slate-800',
  modeInactive: props.isDark
    ? 'bg-white/5 border-white/10 text-slate-400 hover:bg-white/10'
    : 'bg-slate-100/50 border-slate-200/50 text-slate-600 hover:bg-slate-200/50',
  resetBtn: props.isDark
    ? 'bg-white/5 border-white/10 text-slate-400 hover:bg-white/10 hover:text-white'
    : 'bg-slate-100/50 border-slate-200/50 text-slate-600 hover:bg-slate-200/50',
  settingsBtn: props.isDark
    ? 'bg-white/5 border-white/10 text-slate-400 hover:bg-white/10 hover:text-purple-400'
    : 'bg-slate-100/50 border-slate-200/50 text-slate-600 hover:bg-slate-200/50 hover:text-purple-600',
  statCard: props.isDark
    ? 'bg-white/5'
    : 'bg-slate-100/50',
  label: props.isDark ? 'text-slate-400' : 'text-slate-500',
  value: props.isDark ? 'text-white' : 'text-slate-800',
  slider: props.isDark
    ? 'bg-white/10 accent-purple-500'
    : 'bg-slate-200/50 accent-purple-500',
  toggleBg: props.isDark ? 'bg-white/20' : 'bg-slate-300',
  todoText: props.isDark ? 'text-white' : 'text-slate-800',
  taskItem: props.isDark
    ? 'bg-white/5 border-white/10 hover:bg-white/[0.08]'
    : 'bg-slate-100/50 border-slate-200/50 hover:bg-slate-200/50',
  closeBtn: props.isDark
    ? 'text-slate-400 hover:text-white hover:bg-white/10'
    : 'text-slate-500 hover:text-slate-800 hover:bg-slate-200/50'
}))

const switchMode = (mode) => {
  currentMode.value = mode
  resetTimer()
}

const toggleTimer = () => {
  if (isRunning.value && !isPaused.value) {
    // Pause
    isPaused.value = true
    clearInterval(timerInterval.value)
  } else {
    // Start or Resume
    isRunning.value = true
    isPaused.value = false
    timerInterval.value = setInterval(() => {
      if (timeLeft.value > 0) {
        timeLeft.value--
      } else {
        completeTimer()
      }
    }, 1000)
  }
}

const resetTimer = () => {
  isRunning.value = false
  isPaused.value = false
  clearInterval(timerInterval.value)
  timeLeft.value = currentModeData.value.duration * 60
}

const completeTimer = () => {
  clearInterval(timerInterval.value)
  isRunning.value = false
  isPaused.value = false

  if (currentMode.value === 'focus') {
    completedSessions.value++
    localStorage.setItem('completedSessions', completedSessions.value)

    // Complete selected task if any
    if (selectedTask.value) {
      emit('complete-task', selectedTask.value)
      selectedTask.value = null
    }

    // Auto-start break if enabled
    if (settings.value.autoStartBreaks) {
      setTimeout(() => {
        if (completedSessions.value % 4 === 0) {
          switchMode('longBreak')
        } else {
          switchMode('shortBreak')
        }
        toggleTimer()
      }, 1000)
    }
  }

  // Play completion sound
  playCompletionSound()
}

const updateSettings = () => {
  localStorage.setItem('focusSettings', JSON.stringify(settings.value))
  if (!isRunning.value) {
    timeLeft.value = currentModeData.value.duration * 60
  }
}

const playCompletionSound = () => {
  const audioContext = new (window.AudioContext || window.webkitAudioContext)()
  const oscillator = audioContext.createOscillator()
  const gainNode = audioContext.createGain()

  oscillator.connect(gainNode)
  gainNode.connect(audioContext.destination)

  oscillator.frequency.setValueAtTime(523.25, audioContext.currentTime)
  oscillator.frequency.setValueAtTime(659.25, audioContext.currentTime + 0.1)
  oscillator.frequency.setValueAtTime(783.99, audioContext.currentTime + 0.2)

  gainNode.gain.setValueAtTime(0.3, audioContext.currentTime)
  gainNode.gain.exponentialRampToValueAtTime(0.01, audioContext.currentTime + 0.5)

  oscillator.start(audioContext.currentTime)
  oscillator.stop(audioContext.currentTime + 0.5)
}

const getCategoryClass = (category) => {
  const classes = {
    work: props.isDark ? 'bg-blue-500/20 text-blue-300' : 'bg-blue-100 text-blue-600',
    personal: props.isDark ? 'bg-purple-500/20 text-purple-300' : 'bg-purple-100 text-purple-600',
    shopping: props.isDark ? 'bg-emerald-500/20 text-emerald-300' : 'bg-emerald-100 text-emerald-600',
    health: props.isDark ? 'bg-rose-500/20 text-rose-300' : 'bg-rose-100 text-rose-600'
  }
  return classes[category] || (props.isDark ? 'bg-slate-700 text-slate-300' : 'bg-slate-200 text-slate-600')
}

const getCategoryIcon = (category) => {
  const icons = { work: '💼', personal: '🏠', shopping: '🛒', health: '❤️' }
  return icons[category] || '🏠'
}

const getPriorityClass = (priority) => {
  const classes = {
    high: 'bg-red-500/20 text-red-300 border border-red-500/30',
    medium: 'bg-amber-500/20 text-amber-300 border border-amber-500/30'
  }
  return classes[priority] || ''
}

// Watch for mode changes to update timer
watch(currentMode, () => {
  if (!isRunning.value) {
    timeLeft.value = currentModeData.value.duration * 60
  }
})
</script>

<style scoped>
.slide-up-enter-active,
.slide-up-leave-active {
  transition: all 0.3s ease;
}

.slide-up-enter-from,
.slide-up-leave-to {
  opacity: 0;
  transform: translateY(-20px);
}

input[type="range"] {
  -webkit-appearance: none;
  background: transparent;
}

input[type="range"]::-webkit-slider-thumb {
  -webkit-appearance: none;
  width: 16px;
  height: 16px;
  border-radius: 50%;
  background: linear-gradient(135deg, #a855f7, #ec4899);
  cursor: pointer;
  box-shadow: 0 2px 8px rgba(168, 85, 247, 0.4);
}

input[type="range"]::-moz-range-thumb {
  width: 16px;
  height: 16px;
  border-radius: 50%;
  background: linear-gradient(135deg, #a855f7, #ec4899);
  cursor: pointer;
  border: none;
  box-shadow: 0 2px 8px rgba(168, 85, 247, 0.4);
}
</style>
