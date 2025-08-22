<template>
  <div class="bg-gradient-to-r from-blue-600 to-indigo-700 text-white p-4 md:p-6 rounded-t-xl shadow-lg">
    <div class="max-w-4xl mx-auto">
      <h1 class="text-2xl md:text-3xl font-bold text-center mb-6 md:mb-8">Time Interval Planner</h1>
      <div class="grid grid-cols-1 md:grid-cols-4 gap-4 md:gap-6">
        <div class="w-full flex flex-col min-w-[140px] items-center">
          <label for="startTime" class="mb-2 font-semibold text-blue-100">Start Time</label>
          <input
            type="time"
            id="startTime"
            :value="startTime"
            @input="$emit('update:startTime', $event.target.value)"
            class="w-full px-3 py-2 bg-white/10 backdrop-blur border border-white/20 rounded-lg text-white placeholder-white/70 text-sm focus:outline-none focus:border-white/40 focus:bg-white/20 transition-all"
          >
        </div>

        <div class="w-full flex flex-col min-w-[140px] items-center">
          <label for="endTime" class="mb-2 font-semibold text-blue-100">End Time</label>
          <input
            type="time"
            id="endTime"
            :value="endTime"
            @input="$emit('update:endTime', $event.target.value)"
            class="w-full px-3 py-2 bg-white/10 backdrop-blur border border-white/20 rounded-lg text-white placeholder-white/70 text-sm focus:outline-none focus:border-white/40 focus:bg-white/20 transition-all"
          >
        </div>

        <div class="w-full flex flex-col min-w-[140px] items-center">
          <label for="interval" class="mb-2 font-semibold text-blue-100">Interval (min)</label>
          <input
            type="number"
            id="interval"
            :value="interval"
            @input="$emit('update:interval', parseInt($event.target.value))"
            min="5"
            max="120"
            step="5"
            class="w-full px-3 py-2 bg-white/10 backdrop-blur border border-white/20 rounded-lg text-white placeholder-white/70 text-sm focus:outline-none focus:border-white/40 focus:bg-white/20 transition-all"
          >
        </div>

        <div class="flex items-end justify-center w-full">
          <button
            @click="$emit('generate')"
            class="w-[140px] px-6 py-2 bg-white text-blue-600 rounded-lg font-semibold text-sm hover:bg-blue-50 transition-all duration-200 transform hover:scale-105 shadow-lg"
          >
            Generate Schedule
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
defineProps({
  startTime: String,
  endTime: String,
  interval: Number
})

defineEmits(['update:startTime', 'update:endTime', 'update:interval', 'generate'])
</script>