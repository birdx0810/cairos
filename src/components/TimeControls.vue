<template>
  <div class="bg-gradient-to-r from-blue-600 to-indigo-700 text-white p-6 rounded-t-xl shadow-lg">
    <h1 class="text-3xl font-bold text-center mb-6">Time Interval Planner</h1>
    <div class="flex flex-wrap items-end gap-4 justify-center">
      <div class="flex flex-col min-w-[140px]">
        <label for="startTime" class="mb-2 font-semibold text-blue-100">Start Time</label>
        <input
          type="time"
          id="startTime"
          :value="startTime"
          @input="$emit('update:startTime', $event.target.value)"
          class="px-3 py-2 bg-white/10 backdrop-blur border border-white/20 rounded-lg text-white placeholder-white/70 text-sm focus:outline-none focus:border-white/40 focus:bg-white/20 transition-all"
        >
      </div>

      <div class="flex flex-col min-w-[140px]">
        <label for="endTime" class="mb-2 font-semibold text-blue-100">End Time</label>
        <input
          type="time"
          id="endTime"
          :value="endTime"
          @input="$emit('update:endTime', $event.target.value)"
          class="px-3 py-2 bg-white/10 backdrop-blur border border-white/20 rounded-lg text-white placeholder-white/70 text-sm focus:outline-none focus:border-white/40 focus:bg-white/20 transition-all"
        >
      </div>

      <div class="flex flex-col min-w-[120px]">
        <label for="interval" class="mb-2 font-semibold text-blue-100">Interval (min)</label>
        <input
          type="number"
          id="interval"
          :value="interval"
          @input="$emit('update:interval', parseInt($event.target.value))"
          min="1"
          class="px-3 py-2 bg-white/10 backdrop-blur border border-white/20 rounded-lg text-white placeholder-white/70 text-sm focus:outline-none focus:border-white/40 focus:bg-white/20 transition-all"
        >
      </div>

      <button
        @click="$emit('generate')"
        class="px-6 py-2 bg-white text-blue-600 rounded-lg font-semibold text-sm hover:bg-blue-50 transition-all duration-200 transform hover:scale-105 shadow-lg"
      >
        Generate Schedule
      </button>
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