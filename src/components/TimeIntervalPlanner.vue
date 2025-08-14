<template>
  <div class="min-h-screen bg-gradient-to-br from-blue-50 via-white to-indigo-50">
    <div class="max-w-7xl mx-auto p-6">
      <!-- Header -->
      <TimeControls
        v-model:start-time="startTime"
        v-model:end-time="endTime"
        v-model:interval="interval"
        @generate="generateIntervals"
      />

      <!-- Main Content Area -->
      <div class="flex gap-6 mt-6 h-[700px]">
        <!-- Schedule Section (60%) -->
        <div class="flex-1 w-3/5">
          <ScheduleTable
            :schedule-data="scheduleData"
            @update-item="updateScheduleItem"
          />
        </div>

        <!-- Summary Section (30%) -->
        <div class="w-2/5">
          <SummaryTable
            :schedule-data="scheduleData"
          />
        </div>
      </div>
    </div>
  </div>
</template>

<!-- Custom Scrollbar Styles -->
<style>
  .custom-scrollbar::-webkit-scrollbar {
    width: 6px;
  }

  .custom-scrollbar::-webkit-scrollbar-track {
    background: #f1f5f9;
    border-radius: 10px;
  }

  .custom-scrollbar::-webkit-scrollbar-thumb {
    background: linear-gradient(to bottom, #60a5fa, #3b82f6);
    border-radius: 10px;
  }

  .custom-scrollbar::-webkit-scrollbar-thumb:hover {
    background: linear-gradient(to bottom, #3b82f6, #1d4ed8);
  }
</style>

<script setup>
import { ref, onMounted, watch } from 'vue'
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

  const existingData = scheduleData.value
  scheduleData.value = newScheduleData.map(newItem => {
    // Try to find matching time slot in existing data
    const existingItem = existingData.find(item => 
      item.startTime === newItem.startTime && 
      item.endTime === newItem.endTime
    )
    // Keep existing plan/outcome if time slot matches
    return existingItem || newItem
  })
  saveToLocalStorage()
}

const updateScheduleItem = (index, field, value) => {
  scheduleData.value[index][field] = value
  saveToLocalStorage()
}

const exportData = () => {
  return scheduleData.value
}

// Local storage functions
const saveToLocalStorage = () => {
  const dataToSave = {
    settings: {
      startTime: startTime.value,
      endTime: endTime.value,
      interval: interval.value
    },
    scheduleItems: scheduleData.value
  }
  localStorage.setItem('cairos-schedule', JSON.stringify(dataToSave))
}

const loadFromLocalStorage = () => {
  const savedData = localStorage.getItem('cairos-schedule')
  if (savedData) {
    try {
      const parsedData = JSON.parse(savedData)
      startTime.value = parsedData.settings.startTime
      endTime.value = parsedData.settings.endTime
      interval.value = parsedData.settings.interval
      scheduleData.value = parsedData.scheduleItems
      return true
    } catch (e) {
      console.error('Error loading saved data:', e)
      return false
    }
  }
  return false
}

// Watch for changes in time controls
watch([startTime, endTime, interval], () => {
  saveToLocalStorage()
})

// Initialize
onMounted(() => {
  if (!loadFromLocalStorage()) {
    generateIntervals() // Only generate default if no saved data exists
  }
})

// Expose methods to parent component if needed
defineExpose({
  exportData,
  generateIntervals
})
</script>