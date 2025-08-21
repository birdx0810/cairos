<script setup>
import { ref } from 'vue'
import TimeControls from './TimeControls.vue'
import ScheduleTable from './ScheduleTable.vue'
import SummaryTable from './SummaryTable.vue'

// Reactive data
const startTime = ref('08:30')
const endTime = ref('18:00')
const interval = ref(15)
const scheduleData = ref([])

// Utility functions
const timeToMinutes = (timeStr) => {
  const [hours, minutes] = timeStr.split(':').map(Number)
  return hours * 60 + minutes
}

const minutesToTime = (minutes) => {
  const hours = Math.floor(minutes / 60)
  const mins = minutes % 60
  return `${hours.toString().padStart(2, '0')}:${mins.toString().padStart(2, '0')}`
}

// Methods
const generateIntervals = () => {
  if (!startTime.value || !endTime.value || !interval.value) {
    alert('Please fill in all fields')
    return
  }

  const startMinutes = timeToMinutes(startTime.value)
  const endMinutes = timeToMinutes(endTime.value)

  if (startMinutes >= endMinutes) {
    alert('End time must be after start time')
    return
  }

  const newScheduleData = []
  let currentStart = startMinutes

  while (currentStart < endMinutes) {
    let currentEnd = Math.min(currentStart + interval.value, endMinutes)

    newScheduleData.push({
      startTime: minutesToTime(currentStart),
      endTime: minutesToTime(currentEnd),
      plan: '',
      outcome: ''
    })

    currentStart = currentEnd
  }

  scheduleData.value = newScheduleData
}

const updateScheduleItem = (index, field, value) => {
  scheduleData.value[index][field] = value
}

// Initialize scheduleData with an empty array
scheduleData.value = []

// Initialize with default schedule
setTimeout(() => {
  generateIntervals()
}, 0)
</script>

<template>
  <div class="w-full">
    <div class="max-w-7xl mx-auto p-4 md:p-6">
      <!-- Header -->
      <TimeControls
        v-model:start-time="startTime"
        v-model:end-time="endTime"
        v-model:interval="interval"
        @generate="generateIntervals"
      />

      <!-- Main Content Area -->
      <div class="flex flex-col lg:flex-row gap-4 lg:gap-6 mt-4 lg:mt-6">
        <!-- Schedule Section (Full width on mobile, 60% on desktop) -->
        <div class="w-full lg:w-3/5 min-w-0">
          <ScheduleTable
            :schedule-data="scheduleData"
            @update-item="updateScheduleItem"
          />
        </div>

        <!-- Summary Section (Full width on mobile, 40% on desktop) -->
        <div class="w-full lg:w-2/5 min-w-0">
          <SummaryTable
            :schedule-data="scheduleData"
          />
        </div>
      </div>
    </div>
  </div>
</template>