<template>
  <div class="h-full">
    <div class="bg-white/80 backdrop-blur rounded-lg p-3 md:p-4 border border-gray-200/50 shadow-sm h-full">
      <h2 class="text-base md:text-lg font-semibold text-gray-800 mb-3 md:mb-4 flex items-center gap-2">
        <div class="w-2 h-2 bg-green-500 rounded-full"></div>
        Analytics
      </h2>
      <div class="overflow-y-auto max-h-[500px] md:max-h-[600px] custom-scrollbar">
        <div v-if="summaryData.length === 0" class="text-center py-8 md:py-12">
          <div class="flex flex-col items-center gap-2 md:gap-3">
            <div class="w-12 md:w-16 h-12 md:h-16 bg-gradient-to-br from-green-100 to-blue-100 rounded-full flex items-center justify-center">
            </div>
            <div class="text-gray-500 text-xs md:text-sm">
              <div class="font-medium">No data yet</div>
              <div>Add some plans to see analytics</div>
            </div>
          </div>
        </div>

        <div v-else class="space-y-3 md:space-y-4">
          <div
            v-for="(summary, index) in summaryData"
            :key="index"
            class="bg-white rounded-lg border border-gray-200 p-3 md:p-4 hover:shadow-md transition-all duration-200"
          >
            <div class="flex items-center justify-between mb-2 md:mb-3">
              <h3 class="font-semibold text-gray-800 truncate flex-1 mr-2 text-sm md:text-base">
                {{ summary.plan || 'Unnamed Plan' }}
              </h3>
              <div
                :class="[
                  'text-base md:text-lg font-bold px-2 py-1 rounded-lg text-center min-w-[50px] md:min-w-[60px]',
                  summary.executedPercentage === 0 ? 'text-gray-500 bg-gray-100' :
                  summary.executedPercentage < 50 ? 'text-red-600 bg-red-100' :
                  summary.executedPercentage < 100 ? 'text-yellow-600 bg-yellow-100' :
                  summary.executedPercentage === 100 ? 'text-green-600 bg-green-100' :
                  'text-blue-600 bg-blue-100'
                ]"
              >
                {{ summary.executedPercentage }}%
              </div>
            </div>

            <div class="space-y-1.5 md:space-y-2 text-xs md:text-sm text-gray-600">
              <div class="flex justify-between">
                <span>Planned Duration:</span>
                <span class="font-mono">{{ summary.totalDuration }}min</span>
              </div>
              <div class="flex justify-between">
                <span>Time Range:</span>
                <span class="font-mono text-[11px] md:text-sm">{{ summary.startTime }} - {{ summary.endTime }}</span>
              </div>
              <div class="flex justify-between">
                <span>Actual Duration:</span>
                <span class="font-mono font-semibold">{{ summary.executedDuration }}min</span>
              </div>
            </div>

            <div class="mt-2 md:mt-3">
              <div class="flex items-center justify-between text-[10px] md:text-xs text-gray-500 mb-1">
                <span>Execution</span>
                <span>{{ summary.executedPercentage }}%</span>
              </div>
              <div class="w-full bg-gray-200 rounded-full h-1.5 md:h-2">
                <div
                  :class="[
                    'h-2 rounded-full transition-all duration-500',
                    summary.executedPercentage === 0 ? 'bg-gray-400' :
                    summary.executedPercentage < 50 ? 'bg-gradient-to-r from-red-400 to-red-500' :
                    summary.executedPercentage < 100 ? 'bg-gradient-to-r from-yellow-400 to-yellow-500' :
                    summary.executedPercentage === 100 ? 'bg-gradient-to-r from-green-400 to-green-500' :
                    'bg-gradient-to-r from-blue-400 to-blue-500'
                  ]"
                  :style="{ width: Math.min(summary.executedPercentage, 100) + '%' }"
                ></div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed } from 'vue'

const props = defineProps({
  scheduleData: Array
})

const timeToMinutes = (timeStr) => {
  const [hours, minutes] = timeStr.split(':').map(Number)
  return hours * 60 + minutes
}

const summaryData = computed(() => {
  if (!props.scheduleData || props.scheduleData.length === 0) return []

  // First pass: collect all unique plans and calculate their planned duration
  const planGroups = {}

  props.scheduleData.forEach(item => {
    if (!item.plan.trim()) return // Skip empty plans

    const planName = item.plan.trim()
    const startMinutes = timeToMinutes(item.startTime)
    const endMinutes = timeToMinutes(item.endTime)
    const duration = endMinutes - startMinutes

    if (!planGroups[planName]) {
      planGroups[planName] = {
        plan: planName,
        plannedDuration: 0,
        actualDuration: 0,
        slots: []
      }
    }

    planGroups[planName].plannedDuration += duration
    planGroups[planName].slots.push({
      startTime: item.startTime,
      endTime: item.endTime,
      duration
    })
  })

  // Second pass: calculate actual duration based on outcomes
  props.scheduleData.forEach(item => {
    if (!item.outcome.trim()) return // Skip empty outcomes

    const outcomeName = item.outcome.trim()
    const startMinutes = timeToMinutes(item.startTime)
    const endMinutes = timeToMinutes(item.endTime)
    const duration = endMinutes - startMinutes

    // Find the plan group that matches this outcome
    Object.values(planGroups).forEach(group => {
      if (group.plan.toLowerCase() === outcomeName.toLowerCase()) {
        group.actualDuration += duration
      }
    })
  })

  // Convert to summary array
  return Object.values(planGroups).map(group => {
    // Find earliest start and latest end from planned slots
    const startTimes = group.slots.map(slot => timeToMinutes(slot.startTime))
    const endTimes = group.slots.map(slot => timeToMinutes(slot.endTime))

    const earliestStart = Math.min(...startTimes)
    const latestEnd = Math.max(...endTimes)

    const executedPercentage = group.plannedDuration > 0
      ? Math.round((group.actualDuration / group.plannedDuration) * 100)
      : 0

    return {
      plan: group.plan,
      totalDuration: group.plannedDuration,
      executedDuration: group.actualDuration,
      startTime: `${Math.floor(earliestStart / 60).toString().padStart(2, '0')}:${(earliestStart % 60).toString().padStart(2, '0')}`,
      endTime: `${Math.floor(latestEnd / 60).toString().padStart(2, '0')}:${(latestEnd % 60).toString().padStart(2, '0')}`,
      executedPercentage
    }
  }).sort((a, b) => timeToMinutes(a.startTime) - timeToMinutes(b.startTime))
})
</script>