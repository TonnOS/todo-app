<template>
  <div :class="['min-h-screen font-sans selection:bg-purple-500/30 overflow-hidden transition-colors duration-500', themeClasses.app]">
    <!-- Animated Gradient Background -->
    <div :class="['fixed inset-0 pointer-events-none transition-all duration-500', themeClasses.gradientBg]"></div>
    <div :class="['fixed inset-0 pointer-events-none transition-all duration-500', themeClasses.radialBg]"></div>
    
    <!-- Animated Background Orbs -->
    <div :class="['fixed top-20 left-10 w-72 h-72 rounded-full blur-[120px] animate-pulse pointer-events-none transition-colors duration-500', themeClasses.orb1]"></div>
    <div :class="['fixed bottom-40 right-10 w-96 h-96 rounded-full blur-[100px] animate-pulse pointer-events-none transition-colors duration-500', themeClasses.orb2]" style="animation-delay: 1s;"></div>

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
              <p :class="['text-sm font-medium transition-colors', themeClasses.dateText]">{{ currentDate }}</p>
              <h1 :class="['text-2xl font-bold bg-clip-text text-transparent bg-gradient-to-r', themeClasses.greeting]">
                {{ greeting }}
              </h1>
            </div>
          </div>
          
          <!-- Progress Stats -->
          <div class="flex items-center gap-4 mt-4">
            <div :class="['flex items-center gap-2 backdrop-blur-sm rounded-full px-4 py-2 border transition-colors', themeClasses.progressBadge]">
              <div class="w-2 h-2 rounded-full bg-emerald-400 animate-pulse"></div>
              <span :class="['text-sm', themeClasses.progressText]">{{ doneCount }}/{{ todos.length }} done</span>
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

        <!-- Avatar & Theme Toggle -->
        <div class="flex items-center gap-3">
          <!-- Theme Toggle Button -->
          <button 
            @click="toggleTheme"
            :class="['w-10 h-10 rounded-xl flex items-center justify-center transition-all duration-300 border', themeClasses.themeToggle]"
          >
            <svg v-if="isDark" class="w-5 h-5 text-amber-300" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 3v1m0 16v1m9-9h-1M4 12H3m15.364 6.364l-.707-.707M6.343 6.343l-.707-.707m12.728 0l-.707.707M6.343 17.657l-.707.707M16 12a4 4 0 11-8 0 4 4 0 018 0z" />
            </svg>
            <svg v-else class="w-5 h-5 text-slate-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M20.354 15.354A9 9 0 018.646 3.646 9.003 9.003 0 0012 21a9.003 9.003 0 008.354-5.646z" />
            </svg>
          </button>

          <!-- Avatar -->
          <div class="w-12 h-12 rounded-2xl bg-gradient-to-tr from-purple-500 to-pink-500 p-[2px] shadow-lg shadow-purple-500/30">
            <div class="w-full h-full rounded-2xl bg-slate-900 flex items-center justify-center overflow-hidden">
              <span class="text-sm font-bold bg-gradient-to-r from-purple-400 to-pink-400 bg-clip-text text-transparent">OC</span>
            </div>
          </div>
        </div>
      </div>

      <!-- Progress Bar -->
      <div :class="['relative h-2 w-full rounded-full overflow-hidden transition-colors', themeClasses.progressBarBg]">
        <div 
          class="h-full bg-gradient-to-r from-purple-500 via-pink-500 to-rose-500 transition-all duration-700 ease-out rounded-full"
          :style="{ width: progressWidth }"
        >
          <div class="absolute inset-0 bg-gradient-to-r from-transparent via-white/30 to-transparent animate-shimmer"></div>
        </div>
      </div>

      <!-- Category Filter -->
      <div class="flex items-center gap-2 mt-4 overflow-x-auto no-scrollbar pb-2">
        <button 
          v-for="cat in categories" 
          :key="cat.value"
          @click="selectedCategory = cat.value"
          :class="['flex items-center gap-1.5 px-3 py-1.5 rounded-full text-xs font-medium whitespace-nowrap transition-all border', 
            selectedCategory === cat.value ? cat.activeClass : themeClasses.categoryInactive]"
        >
          <span v-if="cat.icon" class="text-sm">{{ cat.icon }}</span>
          {{ cat.label }}
          <span v-if="cat.value !== 'all'" :class="['text-[10px] px-1.5 py-0.5 rounded-full', themeClasses.categoryCount]">
            {{ getCategoryCount(cat.value) }}
          </span>
        </button>
      </div>
    </header>

    <!-- Main Content -->
    <main class="relative px-5 pb-32 max-w-lg mx-auto min-h-[calc(100vh-200px)]">
      <!-- Dynamic View Content -->
      <div v-if="activeNav === 0">
        <!-- View Toggle (Tasks/Archive) -->
        <div class="flex items-center justify-between mb-4">
          <div class="flex items-center gap-2">
            <button 
              @click="currentView = 'tasks'"
              :class="['px-4 py-2 rounded-xl text-sm font-medium transition-all', 
                currentView === 'tasks' ? 'bg-purple-500/20 text-purple-300 border border-purple-500/30' : themeClasses.viewInactive]"
            >
              Tasks
            </button>
            <button 
              @click="currentView = 'archive'"
              :class="['px-4 py-2 rounded-xl text-sm font-medium transition-all flex items-center gap-2', 
                currentView === 'archive' ? 'bg-emerald-500/20 text-emerald-300 border border-emerald-500/30' : themeClasses.viewInactive]"
            >
              Archive
              <span v-if="archivedTodos.length > 0" :class="['text-xs px-2 py-0.5 rounded-full', themeClasses.archiveCount]">
                {{ archivedTodos.length }}
              </span>
            </button>
          </div>
          
          <!-- Clear Archive Button -->
          <button 
            v-if="currentView === 'archive' && archivedTodos.length > 0"
            @click="clearArchive"
            class="text-xs text-red-400 hover:text-red-300 transition-colors flex items-center gap-1"
          >
            <svg class="w-3 h-3" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16" />
            </svg>
            Clear All
          </button>
        </div>

        <!-- Tasks View -->
        <div v-if="currentView === 'tasks'">
          <!-- Draggable Todo List -->
        <draggable 
          v-model="filteredTodos"
          item-key="id"
          handle=".drag-handle"
          :animation="300"
          ghost-class="ghost-item"
          drag-class="dragging-item"
          class="space-y-3"
          @end="onDragEnd"
        >
          <template #item="{ element: todo, index }">
            <div 
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
                :class="['relative backdrop-blur-xl rounded-2xl p-4 border shadow-xl transition-all duration-300 hover:shadow-2xl group', themeClasses.card, swipedIndex === index ? 'translate-x-[-80px]' : 'translate-x-0']"
                :style="{ transform: swipedIndex === index ? 'translateX(-80px)' : getTransform(index) }"
              >
                <div class="flex items-center gap-3">
                  <!-- Drag Handle -->
                  <div class="drag-handle cursor-grab active:cursor-grabbing p-1 -ml-1 opacity-0 group-hover:opacity-100 transition-opacity">
                    <svg :class="['w-4 h-4', themeClasses.dragHandle]" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 8h16M4 16h16" />
                    </svg>
                  </div>

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
                        :class="todo.done ? 'line-through text-slate-500' : themeClasses.todoText"
                      >
                        {{ todo.text }}
                      </span>
                    </div>
                    <div class="flex items-center gap-2 mt-1 flex-wrap">
                      <div v-if="todo.dueTime" class="flex items-center gap-1">
                        <svg :class="['w-3 h-3', themeClasses.timeIcon]" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8v4l3 3m6-3a9 9 0 11-18 0 9 9 0 0118 0z" />
                        </svg>
                        <span :class="['text-xs', themeClasses.timeText]">{{ todo.dueTime }}</span>
                      </div>
                      <!-- Category Badge -->
                      <div 
                        v-if="todo.category"
                        class="flex items-center gap-1 px-2 py-0.5 rounded-full text-[10px] font-medium"
                        :class="getCategoryClass(todo.category)"
                      >
                        <span>{{ getCategoryIcon(todo.category) }}</span>
                        {{ todo.category }}
                      </div>
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
                    @click="removeTodo(todo)"
                    class="opacity-0 group-hover:opacity-100 transition-all p-2 shrink-0"
                    :class="themeClasses.deleteBtn"
                  >
                    <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16" />
                    </svg>
                  </button>
                </div>
              </div>
            </div>
          </template>
        </draggable>

        <!-- Empty State -->
        <transition name="float">
          <div v-if="filteredTodos.length === 0 && currentView === 'tasks'" class="flex flex-col items-center justify-center py-12 text-center relative">
            <div class="relative">
              <div class="absolute -top-8 -left-12 w-6 h-6 rounded-full bg-purple-500/30 animate-float blur-sm" style="animation-delay: 0s;"></div>
              <div class="absolute -top-4 -right-8 w-4 h-4 rounded-full bg-pink-500/30 animate-float blur-sm" style="animation-delay: 0.5s;"></div>
              <div class="absolute top-16 -left-16 w-3 h-3 rounded-full bg-purple-400/20 animate-float blur-sm" style="animation-delay: 1s;"></div>
              <div class="absolute top-20 -right-12 w-5 h-5 rounded-full bg-pink-400/20 animate-float blur-sm" style="animation-delay: 1.5s;"></div>
              
              <div class="w-28 h-28 rounded-3xl bg-gradient-to-br from-purple-500/20 to-pink-500/20 backdrop-blur-sm flex items-center justify-center mb-6 animate-float border border-white/10 shadow-2xl shadow-purple-500/10">
                <div class="relative">
                  <svg class="w-14 h-14 text-purple-300" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2m-6 9l2 2 4-4" />
                  </svg>
                  <div class="absolute -top-1 -right-1 w-4 h-4 rounded-full bg-emerald-400 animate-pulse"></div>
                </div>
              </div>
            </div>
            
            <h3 :class="['text-xl font-bold mb-2', themeClasses.heading]">All caught up!</h3>
            <p :class="['text-sm max-w-[200px]', themeClasses.subtext]">Your day is clear. Time to relax or add something new.</p>
            
            <button 
              @click="showModal = true"
              class="mt-6 px-6 py-3 rounded-full bg-gradient-to-r from-purple-500 to-pink-500 text-white font-medium text-sm shadow-lg shadow-purple-500/25 hover:shadow-purple-500/40 transition-all active:scale-95"
            >
              Add your first task
            </button>
          </div>
        </transition>

        <!-- Archive Empty State -->
        <transition name="float">
          <div v-if="currentView === 'archive' && archivedTodos.length === 0" class="flex flex-col items-center justify-center py-12 text-center relative">
            <div class="w-24 h-24 rounded-3xl bg-gradient-to-br from-emerald-500/20 to-teal-500/20 backdrop-blur-sm flex items-center justify-center mb-6 border border-white/10">
              <svg class="w-12 h-12 text-emerald-300" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M5 13l4 4L19 7" />
              </svg>
            </div>
            <h3 :class="['text-xl font-bold mb-2', themeClasses.heading]">No completed tasks</h3>
            <p :class="['text-sm max-w-[200px]', themeClasses.subtext]">Complete some tasks to see them here.</p>
          </div>
        </transition>
      </div>

      <!-- Plan View -->
      <div v-else-if="activeNav === 1">
        <PlanView 
          :todos="todos" 
          :isDark="isDark" 
          :themeClasses="themeClasses" 
          @toggle-todo="toggleTodo"
        />
      </div>

      <!-- Focus View -->
      <div v-else-if="activeNav === 2">
        <FocusView 
          :todos="todos" 
          :isDark="isDark" 
          @complete-task="handleFocusTaskComplete"
        />
      </div>

      <!-- Settings View -->
      <div v-else-if="activeNav === 3">
        <SettingsView 
          :isDark="isDark" 
          :themeClasses="themeClasses" 
          :todos="todos"
          :archivedTodos="archivedTodos"
          @toggle-theme="toggleTheme" 
          @clear-data="handleClearData"
          @import-data="handleImportData"
        />
      </div>

      <!-- Pulsing FAB -->
      <div v-if="activeNav === 0 && currentView === 'tasks'" class="fixed bottom-24 right-6 z-40">
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

      <!-- Bottom Navigation -->
      <nav :class="['fixed bottom-0 inset-x-0 h-[88px] backdrop-blur-2xl border-t flex items-center justify-around px-6 pb-6 z-30 transition-colors', themeClasses.navBg, themeClasses.navBorder]">
        <!-- Active Indicator Pill -->
        <div 
          :class="['absolute bottom-6 h-10 rounded-full transition-all duration-500 ease-out', themeClasses.navIndicator]"
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
          :class="activeNav === index ? 'text-white scale-110' : themeClasses.navItem"
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
        <div v-if="showModal" :class="['fixed inset-0 z-50 flex items-end justify-center backdrop-blur-sm transition-colors', themeClasses.modalOverlay]" @click="showModal = false">
          <div :class="['w-full max-w-lg rounded-t-[32px] p-6 border-t shadow-2xl transition-colors', themeClasses.modalBg]" @click.stop>
            <!-- Handle -->
            <div :class="['w-10 h-1 rounded-full mx-auto mb-6', themeClasses.modalHandle]"></div>
            
            <h2 :class="['text-xl font-bold mb-5', themeClasses.heading]">New Task</h2>
            
            <!-- Task Input -->
            <div class="relative mb-4">
              <input 
                v-model="newTodo"
                @keyup.enter="addTodo"
                type="text" 
                placeholder="What needs to be done?" 
                :class="['w-full rounded-2xl px-5 py-4 focus:outline-none focus:ring-2 transition-all text-base', themeClasses.input]"
                ref="taskInput"
              />
            </div>

            <!-- Category Selection -->
            <div class="mb-4">
              <label :class="['text-xs font-medium mb-2 block', themeClasses.label]">Category</label>
              <div class="flex gap-2 flex-wrap">
                <button 
                  v-for="cat in categoryOptions" 
                  :key="cat.value"
                  @click="selectedCategoryNew = cat.value"
                  :class="['flex items-center gap-1.5 px-3 py-2 rounded-xl text-xs font-medium transition-all border', 
                    selectedCategoryNew === cat.value ? cat.activeClass : themeClasses.categoryBtn]"
                >
                  <span class="text-sm">{{ cat.icon }}</span>
                  {{ cat.label }}
                </button>
              </div>
            </div>

            <!-- Priority Selection -->
            <div class="mb-5">
              <label :class="['text-xs font-medium mb-2 block', themeClasses.label]">Priority</label>
              <div class="flex gap-2">
                <button 
                  v-for="p in priorities" 
                  :key="p.value"
                  @click="selectedPriority = p.value"
                  :class="['flex-1 py-3 rounded-xl text-sm font-medium transition-all border', 
                    selectedPriority === p.value ? p.activeClass : themeClasses.priorityBtn]"
                >
                  {{ p.label }}
                </button>
              </div>
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
    </main>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, nextTick, watch, defineAsyncComponent } from 'vue'
import confetti from 'canvas-confetti'
import draggable from 'vuedraggable'

// Async imports for views
const PlanView = defineAsyncComponent(() => import('../views/PlanView.vue'))
const FocusView = defineAsyncComponent(() => import('../views/FocusView.vue'))
const SettingsView = defineAsyncComponent(() => import('../views/SettingsView.vue'))

// Theme Management
const isDark = ref(true)

const themeClasses = computed(() => {
  if (isDark.value) {
    return {
      app: 'bg-slate-950 text-slate-100',
      gradientBg: 'bg-gradient-to-br from-slate-950 via-purple-950/30 to-pink-950/20',
      radialBg: 'bg-[radial-gradient(ellipse_at_top,_var(--tw-gradient-stops))] from-purple-900/20 via-transparent to-transparent',
      orb1: 'bg-purple-500/20',
      orb2: 'bg-pink-500/10',
      card: 'bg-white/[0.08] border-white/10 hover:bg-white/[0.12]',
      heading: 'text-white',
      subtext: 'text-slate-400',
      dateText: 'text-slate-400',
      greeting: 'from-white via-purple-200 to-pink-200',
      progressBadge: 'bg-white/5 border-white/10',
      progressText: 'text-slate-300',
      progressBarBg: 'bg-white/5',
      dragHandle: 'text-slate-500',
      todoText: 'text-white',
      timeIcon: 'text-slate-500',
      timeText: 'text-slate-500',
      deleteBtn: 'text-slate-500 hover:text-red-400',
      categoryInactive: 'bg-white/5 border-white/10 text-slate-400 hover:bg-white/10',
      categoryCount: 'bg-white/10 text-slate-300',
      viewInactive: 'bg-white/5 text-slate-400 border border-white/10 hover:bg-white/10',
      archiveCount: 'bg-emerald-500/20 text-emerald-300',
      navBg: 'bg-slate-950/80',
      navBorder: 'border-white/5',
      navIndicator: 'bg-gradient-to-r from-purple-500/20 to-pink-500/20 border border-white/10',
      navItem: 'text-slate-500 hover:text-slate-300',
      modalOverlay: 'bg-slate-950/60',
      modalBg: 'bg-slate-900 border-white/10',
      modalHandle: 'bg-white/20',
      input: 'bg-white/5 border-white/10 text-white placeholder-slate-500 focus:ring-purple-500/50 focus:border-purple-500/30',
      label: 'text-slate-400',
      categoryBtn: 'bg-white/5 border-white/10 text-slate-400 hover:bg-white/10',
      priorityBtn: 'bg-white/5 border-white/10 text-slate-400 hover:bg-white/10'
    }
  } else {
    return {
      app: 'bg-slate-50 text-slate-800',
      gradientBg: 'bg-gradient-to-br from-slate-50 via-purple-100/50 to-pink-100/30',
      radialBg: 'bg-[radial-gradient(ellipse_at_top,_var(--tw-gradient-stops))] from-purple-200/30 via-transparent to-transparent',
      orb1: 'bg-purple-400/20',
      orb2: 'bg-pink-400/15',
      card: 'bg-white/80 border-slate-200/50 hover:bg-white/95 shadow-slate-200/50',
      heading: 'text-slate-800',
      subtext: 'text-slate-500',
      dateText: 'text-slate-500',
      greeting: 'from-slate-800 via-purple-600 to-pink-600',
      progressBadge: 'bg-white/80 border-slate-200/50',
      progressText: 'text-slate-600',
      progressBarBg: 'bg-slate-200/50',
      dragHandle: 'text-slate-400',
      todoText: 'text-slate-800',
      timeIcon: 'text-slate-400',
      timeText: 'text-slate-400',
      deleteBtn: 'text-slate-400 hover:text-red-500',
      categoryInactive: 'bg-white/60 border-slate-200/50 text-slate-500 hover:bg-white/80',
      categoryCount: 'bg-slate-200/50 text-slate-600',
      viewInactive: 'bg-white/60 text-slate-500 border border-slate-200/50 hover:bg-white/80',
      archiveCount: 'bg-emerald-100 text-emerald-600',
      navBg: 'bg-white/80',
      navBorder: 'border-slate-200/50',
      navIndicator: 'bg-gradient-to-r from-purple-500/20 to-pink-500/20 border border-slate-200/50',
      navItem: 'text-slate-500 hover:text-slate-700',
      modalOverlay: 'bg-slate-900/40',
      modalBg: 'bg-white border-slate-200/50',
      modalHandle: 'bg-slate-300',
      input: 'bg-slate-100/50 border-slate-200/50 text-slate-800 placeholder-slate-400 focus:ring-purple-500/30 focus:border-purple-500/30',
      label: 'text-slate-500',
      categoryBtn: 'bg-slate-100/50 border-slate-200/50 text-slate-600 hover:bg-slate-200/50',
      priorityBtn: 'bg-slate-100/50 border-slate-200/50 text-slate-600 hover:bg-slate-200/50'
    }
  }
})

// Data
const todos = ref([])
const archivedTodos = ref([])
const newTodo = ref('')
const showModal = ref(false)
const selectedPriority = ref('normal')
const selectedCategory = ref('all')
const selectedCategoryNew = ref('personal')
const taskInput = ref(null)
const activeNav = ref(0)
const swipedIndex = ref(null)
const touchStartX = ref(0)
const touchCurrentX = ref(0)
const navRefs = ref([])
const currentView = ref('tasks')

// Categories
const categories = [
  { value: 'all', label: 'All', icon: '', activeClass: 'bg-gradient-to-r from-purple-500 to-pink-500 border-purple-400 text-white' },
  { value: 'work', label: 'Work', icon: '💼', activeClass: 'bg-gradient-to-r from-blue-500 to-cyan-500 border-blue-400 text-white' },
  { value: 'personal', label: 'Personal', icon: '🏠', activeClass: 'bg-gradient-to-r from-purple-500 to-pink-500 border-purple-400 text-white' },
  { value: 'shopping', label: 'Shopping', icon: '🛒', activeClass: 'bg-gradient-to-r from-emerald-500 to-teal-500 border-emerald-400 text-white' },
  { value: 'health', label: 'Health', icon: '❤️', activeClass: 'bg-gradient-to-r from-rose-500 to-red-500 border-rose-400 text-white' }
]

const categoryOptions = categories.filter(c => c.value !== 'all')

// Priorities
const priorities = [
  { value: 'normal', label: 'Normal', activeClass: isDark.value ? 'bg-slate-600 border-slate-500 text-white' : 'bg-slate-400 border-slate-300 text-white' },
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

const filteredTodos = computed({
  get() {
    let result = todos.value
    
    // Filter by category
    if (selectedCategory.value !== 'all') {
      result = result.filter(t => t.category === selectedCategory.value)
    }
    
    // Sort: incomplete first, then by priority
    return result.sort((a, b) => {
      if (a.done !== b.done) return a.done ? 1 : -1
      const priorityOrder = { high: 0, medium: 1, normal: 2 }
      return priorityOrder[a.priority] - priorityOrder[b.priority]
    })
  },
  set(newValue) {
    // Update todos while preserving archived and filtered out items
    const updatedTodos = [...newValue]
    const archived = archivedTodos.value
    
    // Save to localStorage
    localStorage.setItem('todos', JSON.stringify(updatedTodos))
    localStorage.setItem('archivedTodos', JSON.stringify(archived))
    
    // Update reactive refs
    todos.value = updatedTodos
  }
})

const activeNavPosition = computed(() => {
  const el = navRefs.value[activeNav.value]
  return el ? el.offsetLeft + el.offsetWidth / 2 : 0
})

// Methods
const toggleTheme = () => {
  isDark.value = !isDark.value
  localStorage.setItem('theme', isDark.value ? 'dark' : 'light')
  playSound('click')
}

const getCategoryClass = (category) => {
  const classes = {
    work: isDark.value 
      ? 'bg-blue-500/20 text-blue-300 border border-blue-500/30' 
      : 'bg-blue-100 text-blue-600 border border-blue-200',
    personal: isDark.value 
      ? 'bg-purple-500/20 text-purple-300 border border-purple-500/30' 
      : 'bg-purple-100 text-purple-600 border border-purple-200',
    shopping: isDark.value 
      ? 'bg-emerald-500/20 text-emerald-300 border border-emerald-500/30' 
      : 'bg-emerald-100 text-emerald-600 border border-emerald-200',
    health: isDark.value 
      ? 'bg-rose-500/20 text-rose-300 border border-rose-500/30' 
      : 'bg-rose-100 text-rose-600 border border-rose-200'
  }
  return classes[category] || classes.personal
}

const getCategoryIcon = (category) => {
  const icons = { work: '💼', personal: '🏠', shopping: '🛒', health: '❤️' }
  return icons[category] || '🏠'
}

const getCategoryCount = (category) => {
  if (category === 'all') return todos.value.length
  return todos.value.filter(t => t.category === category && !t.done).length
}

const playSound = (type) => {
  const audioContext = new (window.AudioContext || window.webkitAudioContext)()
  const oscillator = audioContext.createOscillator()
  const gainNode = audioContext.createGain()
  
  oscillator.connect(gainNode)
  gainNode.connect(audioContext.destination)
  
  switch(type) {
    case 'create':
      oscillator.frequency.setValueAtTime(523.25, audioContext.currentTime) // C5
      oscillator.frequency.exponentialRampToValueAtTime(1046.5, audioContext.currentTime + 0.1)
      gainNode.gain.setValueAtTime(0.3, audioContext.currentTime)
      gainNode.gain.exponentialRampToValueAtTime(0.01, audioContext.currentTime + 0.3)
      oscillator.start(audioContext.currentTime)
      oscillator.stop(audioContext.currentTime + 0.3)
      break
    case 'complete':
      oscillator.frequency.setValueAtTime(440, audioContext.currentTime) // A4
      oscillator.frequency.setValueAtTime(554.37, audioContext.currentTime + 0.1) // C#5
      oscillator.frequency.setValueAtTime(659.25, audioContext.currentTime + 0.2) // E5
      gainNode.gain.setValueAtTime(0.3, audioContext.currentTime)
      gainNode.gain.exponentialRampToValueAtTime(0.01, audioContext.currentTime + 0.5)
      oscillator.start(audioContext.currentTime)
      oscillator.stop(audioContext.currentTime + 0.5)
      break
    case 'delete':
      oscillator.frequency.setValueAtTime(200, audioContext.currentTime)
      oscillator.frequency.exponentialRampToValueAtTime(50, audioContext.currentTime + 0.2)
      gainNode.gain.setValueAtTime(0.2, audioContext.currentTime)
      gainNode.gain.exponentialRampToValueAtTime(0.01, audioContext.currentTime + 0.2)
      oscillator.start(audioContext.currentTime)
      oscillator.stop(audioContext.currentTime + 0.2)
      break
    case 'click':
      oscillator.frequency.setValueAtTime(800, audioContext.currentTime)
      gainNode.gain.setValueAtTime(0.1, audioContext.currentTime)
      gainNode.gain.exponentialRampToValueAtTime(0.01, audioContext.currentTime + 0.1)
      oscillator.start(audioContext.currentTime)
      oscillator.stop(audioContext.currentTime + 0.1)
      break
  }
}

const toggleTodo = (todo) => {
  const wasDone = todo.done
  todo.done = !wasDone
  
  if (!wasDone && todo.done) {
    playSound('complete')
    triggerConfetti()
    
    // Move to archive after a delay
    setTimeout(() => {
      const index = todos.value.findIndex(t => t.id === todo.id)
      if (index !== -1) {
        const [completedTodo] = todos.value.splice(index, 1)
        completedTodo.completedAt = new Date().toISOString()
        archivedTodos.value.unshift(completedTodo)
        saveData()
      }
    }, 1000)
  }
  
  saveData()
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
    const newTask = {
      id: Date.now(),
      text: newTodo.value.trim(),
      done: false,
      priority: selectedPriority.value,
      category: selectedCategoryNew.value,
      dueTime: new Date().toLocaleTimeString('en-US', { hour: 'numeric', minute: '2-digit' }),
      createdAt: new Date().toISOString()
    }
    
    todos.value.push(newTask)
    playSound('create')
    
    newTodo.value = ''
    selectedPriority.value = 'normal'
    selectedCategoryNew.value = 'personal'
    showModal.value = false
    
    saveData()
  }
}

const removeTodo = (todo) => {
  const index = todos.value.findIndex(t => t.id === todo.id)
  if (index !== -1) {
    todos.value.splice(index, 1)
    playSound('delete')
    swipedIndex.value = null
    saveData()
  }
}

const clearArchive = () => {
  if (confirm('Are you sure you want to clear all archived tasks?')) {
    archivedTodos.value = []
    playSound('delete')
    saveData()
  }
}

const onDragEnd = () => {
  saveData()
}

const handleFocusTaskComplete = (taskId) => {
  const todo = todos.value.find(t => t.id === taskId)
  if (todo) {
    toggleTodo(todo)
  }
}

const handleClearData = () => {
  todos.value = []
  archivedTodos.value = []
  saveData()
  playSound('delete')
}

const handleImportData = (data) => {
  todos.value = data.todos || []
  archivedTodos.value = data.archivedTodos || []
  saveData()
}

const saveData = () => {
  localStorage.setItem('todos', JSON.stringify(todos.value))
  localStorage.setItem('archivedTodos', JSON.stringify(archivedTodos.value))
}

const loadData = () => {
  const savedTodos = localStorage.getItem('todos')
  const savedArchived = localStorage.getItem('archivedTodos')
  const savedTheme = localStorage.getItem('theme')
  
  if (savedTodos) {
    todos.value = JSON.parse(savedTodos)
  } else {
    // Default todos
    todos.value = [
      { id: 1, text: 'Review project proposal', done: false, priority: 'high', category: 'work', dueTime: '2:00 PM', createdAt: new Date().toISOString() },
      { id: 2, text: 'Team standup meeting', done: false, priority: 'normal', category: 'work', dueTime: '10:00 AM', createdAt: new Date().toISOString() },
      { id: 3, text: 'Buy groceries', done: false, priority: 'medium', category: 'shopping', dueTime: '6:00 PM', createdAt: new Date().toISOString() },
      { id: 4, text: 'Morning workout', done: false, priority: 'high', category: 'health', dueTime: '7:00 AM', createdAt: new Date().toISOString() }
    ]
  }
  
  if (savedArchived) {
    archivedTodos.value = JSON.parse(savedArchived)
  }
  
  if (savedTheme) {
    isDark.value = savedTheme === 'dark'
  }
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
    const todo = filteredTodos.value[index]
    if (todo) {
      setTimeout(() => removeTodo(todo), 200)
    }
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
  loadData()
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

/* Drag and Drop styles */
.ghost-item {
  opacity: 0.5;
  background: rgba(168, 85, 247, 0.1) !important;
  border: 2px dashed rgba(168, 85, 247, 0.5) !important;
}

.dragging-item {
  opacity: 0.9;
  transform: scale(1.02);
  box-shadow: 0 20px 40px rgba(0, 0, 0, 0.3);
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

/* Sortable drag styles */
.sortable-ghost {
  opacity: 0.4;
  background: linear-gradient(135deg, rgba(168, 85, 247, 0.2), rgba(236, 72, 153, 0.2)) !important;
}

.sortable-drag {
  opacity: 0.9;
  transform: scale(1.05) !important;
  box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.5) !important;
}
</style>
