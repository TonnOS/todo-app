<template>
  <div class="space-y-4">
    <!-- Day Selector -->
    <div :class="['backdrop-blur-xl rounded-2xl p-4 border shadow-lg', themeClasses.card]">
      <div class="flex items-center justify-between mb-3">
        <h3 :class="['text-sm font-semibold', themeClasses.heading]">Weekly Plan</h3>
        <span :class="['text-xs', themeClasses.subtext]">{{ currentWeekRange }}</span>
      </div>
      <div class="flex justify-between gap-1">
        <button
          v-for="day in weekDays"
          :key="day.date"
          @click="selectedDay = day.date"
          :class="[
            'flex flex-col items-center gap-1 py-2 px-2 rounded-xl transition-all duration-300 min-w-[40px]',
            selectedDay === day.date
              ? 'bg-gradient-to-br from-purple-500 to-pink-500 text-white shadow-lg shadow-purple-500/30 scale-105'
              : themeClasses.dayInactive
          ]"
        >
          <span class="text-[10px] font-medium uppercase">{{ day.shortName }}</span>
          <span class="text-lg font-bold">{{ day.dayNum }}</span>
          <div
            v-if="day.hasTasks"
            class="w-1.5 h-1.5 rounded-full"
            :class="selectedDay === day.date ? 'bg-white' : 'bg-purple-400'"
          ></div>
        </button>
      </div>
    </div>

    <!-- Time Period Filter -->
    <div :class="['backdrop-blur-xl rounded-2xl p-4 border shadow-lg', themeClasses.card]">
      <h3 :class="['text-sm font-semibold mb-3', themeClasses.heading]">Time of Day</h3>
      <div class="grid grid-cols-3 gap-2">
        <button
          v-for="period in timePeriods"
          :key="period.value"
          @click="selectedPeriod = period.value"
          :class="[
            'flex flex-col items-center gap-2 py-3 px-2 rounded-xl transition-all duration-300 border',
            selectedPeriod === period.value
              ? period.activeClass
              : themeClasses.periodInactive
          ]"
        >
          <span class="text-xl">{{ period.icon }}</span>
          <span class="text-xs font-medium">{{ period.label }}</span>
          <span :class="['text-[10px]', themeClasses.subtext]">{{ period.timeRange }}</span>
        </button>
      </div>
    </div>

    <!-- Tasks for Selected Day/Time -->
    <div :class="['backdrop-blur-xl rounded-2xl p-4 border shadow-lg', themeClasses.card]">
      <div class="flex items-center justify-between mb-4">
        <h3 :class="['text-sm font-semibold', themeClasses.heading]">
          {{ formattedSelectedDate }}
        </h3>
        <span :class="['text-xs px-2 py-1 rounded-full', themeClasses.badge]">
          {{ filteredTodos.length }} tasks
        </span>
      </div>

      <!-- Task List -->
      <div v-if="filteredTodos.length > 0" class="space-y-2">
        <div
          v-for="todo in filteredTodos"
          :key="todo.id"
          :class="[
            'flex items-center gap-3 p-3 rounded-xl border transition-all duration-300',
            todo.done
              ? 'bg-emerald-500/10 border-emerald-500/20'
              : themeClasses.taskItem
          ]"
        >
          <button
            @click="toggleTodo(todo)"
            class="relative w-6 h-6 rounded-full border-2 flex items-center justify-center transition-all duration-300 shrink-0"
            :class="todo.done ? 'bg-emerald-500 border-transparent' : 'border-purple-400/50'"
          >
            <svg
              v-if="todo.done"
              class="w-3.5 h-3.5 text-white"
              fill="none"
              stroke="currentColor"
              viewBox="0 0 24 24"
            >
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="3" d="M5 13l4 4L19 7" />
            </svg>
          </button>

          <div class="flex-1 min-w-0">
            <span
              :class="[
                'text-sm transition-all duration-300',
                todo.done ? 'line-through text-slate-500' : themeClasses.todoText
              ]"
            >
              {{ todo.text }}
            </span>
            <div class="flex items-center gap-2 mt-1">
              <span
                v-if="todo.dueTime"
                class="text-[10px] px-1.5 py-0.5 rounded-full"
                :class="getTimePeriodClass(todo.dueTime)"
              >
                {{ todo.dueTime }}
              </span>
              <span
                v-if="todo.category"
                class="text-[10px] px-1.5 py-0.5 rounded-full flex items-center gap-1"
                :class="getCategoryClass(todo.category)"
              >
                <span>{{ getCategoryIcon(todo.category) }}</span>
                {{ todo.category }}
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
        </div>
      </div>

      <!-- Empty State -->
      <div v-else class="flex flex-col items-center justify-center py-8 text-center">
        <div class="w-16 h-16 rounded-2xl bg-gradient-to-br from-purple-500/20 to-pink-500/20 flex items-center justify-center mb-4">
          <svg :class="['w-8 h-8', themeClasses.icon]" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M8 7V3m8 4V3m-9 8h10M5 21h14a2 2 0 002-2V7a2 2 0 00-2-2H5a2 2 0 00-2 2v12a2 2 0 002 2z" />
          </svg>
        </div>
        <p :class="['text-sm font-medium mb-1', themeClasses.heading]">No tasks planned</p>
        <p :class="['text-xs', themeClasses.subtext]">
          {{ selectedPeriod === 'all' ? 'No tasks for this day' : `No ${selectedPeriod} tasks scheduled` }}
        </p>
      </div>
    </div>

    <!-- Weekly Overview Stats -->
    <div :class="['backdrop-blur-xl rounded-2xl p-4 border shadow-lg', themeClasses.card]">
      <h3 :class="['text-sm font-semibold mb-3', themeClasses.heading]">Week Overview</h3>
      <div class="grid grid-cols-3 gap-3">
        <div
          v-for="stat in weekStats"
          :key="stat.label"
          :class="['text-center p-3 rounded-xl', themeClasses.statCard]"
        >
          <p :class="['text-2xl font-bold mb-1', stat.colorClass]">{{ stat.value }}</p>
          <p :class="['text-[10px] uppercase tracking-wide', themeClasses.subtext]">{{ stat.label }}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, watch } from 'vue'

const props = defineProps({
  todos: {
    type: Array,
    required: true
  },
  isDark: {
    type: Boolean,
    required: true
  },
  themeClasses: {
    type: Object,
    required: true
  }
})

const emit = defineEmits(['toggle-todo'])

const selectedDay = ref(new Date().toISOString().split('T')[0])
const selectedPeriod = ref('all')

const weekDays = computed(() => {
  const days = []
  const today = new Date()
  const currentDay = today.getDay()
  const diff = today.getDate() - currentDay + (currentDay === 0 ? -6 : 1)
  const monday = new Date(today.setDate(diff))

  for (let i = 0; i < 7; i++) {
    const date = new Date(monday)
    date.setDate(monday.getDate() + i)
    const dateStr = date.toISOString().split('T')[0]
    const dayTodos = props.todos.filter(t => t.createdAt?.startsWith(dateStr))

    days.push({
      date: dateStr,
      shortName: date.toLocaleDateString('en-US', { weekday: 'narrow' }),
      dayNum: date.getDate(),
      isToday: dateStr === new Date().toISOString().split('T')[0],
      hasTasks: dayTodos.length > 0
    })
  }
  return days
})

const currentWeekRange = computed(() => {
  const start = weekDays.value[0]
  const end = weekDays.value[6]
  const startDate = new Date(start.date)
  const endDate = new Date(end.date)
  return `${startDate.toLocaleDateString('en-US', { month: 'short', day: 'numeric' })} - ${endDate.toLocaleDateString('en-US', { month: 'short', day: 'numeric' })}`
})

const timePeriods = computed(() => [
  {
    value: 'all',
    label: 'All Day',
    icon: '🌅',
    timeRange: '24h',
    activeClass: 'bg-gradient-to-br from-purple-500 to-pink-500 text-white border-purple-400'
  },
  {
    value: 'morning',
    label: 'Morning',
    icon: '🌤️',
    timeRange: '6AM-12PM',
    activeClass: 'bg-gradient-to-br from-amber-400 to-orange-500 text-white border-amber-400'
  },
  {
    value: 'afternoon',
    label: 'Afternoon',
    icon: '☀️',
    timeRange: '12PM-6PM',
    activeClass: 'bg-gradient-to-br from-blue-400 to-cyan-500 text-white border-blue-400'
  },
  {
    value: 'evening',
    label: 'Evening',
    icon: '🌙',
    timeRange: '6PM-12AM',
    activeClass: 'bg-gradient-to-br from-indigo-500 to-purple-600 text-white border-indigo-400'
  }
])

const formattedSelectedDate = computed(() => {
  const date = new Date(selectedDay.value)
  const today = new Date().toISOString().split('T')[0]
  if (selectedDay.value === today) return 'Today'
  return date.toLocaleDateString('en-US', { weekday: 'long', month: 'long', day: 'numeric' })
})

const getTimePeriod = (timeStr) => {
  if (!timeStr) return null
  const hour = parseInt(timeStr.split(':')[0])
  const isPM = timeStr.toLowerCase().includes('pm')
  let hour24 = hour
  if (isPM && hour !== 12) hour24 += 12
  if (!isPM && hour === 12) hour24 = 0

  if (hour24 >= 6 && hour24 < 12) return 'morning'
  if (hour24 >= 12 && hour24 < 18) return 'afternoon'
  if (hour24 >= 18 || hour24 < 6) return 'evening'
  return null
}

const filteredTodos = computed(() => {
  let result = props.todos.filter(t => {
    const taskDate = t.createdAt?.split('T')[0] || new Date().toISOString().split('T')[0]
    return taskDate === selectedDay.value
  })

  if (selectedPeriod.value !== 'all') {
    result = result.filter(t => {
      const period = getTimePeriod(t.dueTime)
      return period === selectedPeriod.value
    })
  }

  return result.sort((a, b) => {
    if (a.done !== b.done) return a.done ? 1 : -1
    return 0
  })
})

const weekStats = computed(() => [
  {
    label: 'Total Tasks',
    value: props.todos.filter(t => !t.done).length,
    colorClass: props.isDark ? 'text-purple-400' : 'text-purple-600'
  },
  {
    label: 'This Week',
    value: props.todos.filter(t => {
      const taskDate = new Date(t.createdAt || new Date())
      const weekStart = new Date(weekDays.value[0].date)
      const weekEnd = new Date(weekDays.value[6].date)
      weekEnd.setDate(weekEnd.getDate() + 1)
      return taskDate >= weekStart && taskDate < weekEnd && !t.done
    }).length,
    colorClass: props.isDark ? 'text-pink-400' : 'text-pink-600'
  },
  {
    label: 'Completed',
    value: props.todos.filter(t => t.done).length,
    colorClass: 'text-emerald-400'
  }
])

const toggleTodo = (todo) => {
  emit('toggle-todo', todo)
}

const getTimePeriodClass = (timeStr) => {
  const period = getTimePeriod(timeStr)
  const classes = {
    morning: props.isDark ? 'bg-amber-500/20 text-amber-300' : 'bg-amber-100 text-amber-600',
    afternoon: props.isDark ? 'bg-blue-500/20 text-blue-300' : 'bg-blue-100 text-blue-600',
    evening: props.isDark ? 'bg-indigo-500/20 text-indigo-300' : 'bg-indigo-100 text-indigo-600'
  }
  return classes[period] || (props.isDark ? 'bg-slate-700 text-slate-300' : 'bg-slate-200 text-slate-600')
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
</script>
