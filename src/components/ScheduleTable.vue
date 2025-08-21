<template>
  <div class="h-full">
    <div class="bg-white/80 backdrop-blur rounded-lg p-3 md:p-4 border border-gray-200/50 shadow-sm h-full">
      <div class="flex flex-col md:flex-row md:justify-between md:items-center gap-3 md:gap-4 mb-4">
        <h2 class="text-base md:text-lg font-semibold text-gray-800 flex items-center gap-2">
          <div class="w-2 h-2 bg-blue-500 rounded-full"></div>
          Schedule
        </h2>
        <div class="flex gap-2 justify-end">
          <button
            @click="clearSchedule"
            class="w-[120px] px-3 py-1.5 text-sm bg-red-500 text-white rounded hover:bg-red-600 transition-colors flex items-center justify-center gap-1"
            :disabled="!hasContent"
          >
            <span>Clear All</span>
          </button>
          <button
            @click="exportToExcel"
            class="w-[120px] px-3 py-1.5 text-sm bg-green-500 text-white rounded hover:bg-green-600 transition-colors flex items-center justify-center gap-1"
            :disabled="!scheduleData.length"
          >
            <span>Export to Excel</span>
          </button>
        </div>
      </div>
      <div class="overflow-x-auto">
        <div class="overflow-y-auto max-h-[500px] md:max-h-[600px] custom-scrollbar">
          <table class="w-full border-collapse">
            <thead class="sticky top-0 bg-white z-10">
              <tr>
                <th class="w-[100px] md:w-[120px] px-2 md:px-3 py-2 md:py-3 text-left text-xs font-semibold text-gray-600 uppercase tracking-wider border-b-2 border-gray-100">Time</th>
                <th class="w-[calc(50%-50px)] md:w-[calc(50%-60px)] px-2 md:px-3 py-2 md:py-3 text-left text-xs font-semibold text-gray-600 uppercase tracking-wider border-b-2 border-gray-100">Plan</th>
                <th class="w-[calc(50%-50px)] md:w-[calc(50%-60px)] px-2 md:px-3 py-2 md:py-3 text-left text-xs font-semibold text-gray-600 uppercase tracking-wider border-b-2 border-gray-100">Outcome</th>
              </tr>
            </thead>
            <tbody>
              <tr v-if="scheduleData.length === 0">
                <td colspan="3" class="text-center text-gray-500 italic py-8 md:py-12">
                  <div class="flex flex-col items-center gap-2">
                    <div class="w-10 md:w-12 h-10 md:h-12 bg-gray-100 rounded-full flex items-center justify-center">
                    </div>
                    <span class="text-sm md:text-base">Generate your schedule to get started</span>
                  </div>
                </td>
              </tr>
              <ScheduleRow
                v-for="(item, index) in scheduleData"
                :key="index"
                :item="item"
                :index="index"
                @update="handleUpdate"
              />
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed } from 'vue'
import * as XLSX from 'xlsx'
import ScheduleRow from './ScheduleRow.vue'

const props = defineProps({
  scheduleData: {
    type: Array,
    default: () => []
  }
})

console.log('ScheduleTable received data:', props.scheduleData)

const emit = defineEmits(['updateItem'])

const hasContent = computed(() => {
  return props.scheduleData.some(item => item.plan || item.outcome)
})

const handleUpdate = (index, field, value) => {
  emit('updateItem', index, field, value)
}

const clearSchedule = () => {
  if (confirm('Are you sure you want to clear all plans and outcomes?')) {
    props.scheduleData.forEach((item, index) => {
      if (item.plan) {
        emit('updateItem', index, 'plan', '')
      }
      if (item.outcome) {
        emit('updateItem', index, 'outcome', '')
      }
    })
  }
}

const exportToExcel = () => {
  // Transform data for Excel
  const excelData = props.scheduleData.map(item => ({
    'Time': `${item.startTime} - ${item.endTime}`,
    'Plan': item.plan || '',
    'Outcome': item.outcome || ''
  }))

  // Create workbook and worksheet
  const wb = XLSX.utils.book_new()
  const ws = XLSX.utils.json_to_sheet(excelData)

  // Set column widths
  const colWidths = [
    { wch: 15 }, // Time column
    { wch: 40 }, // Plan column
    { wch: 40 }  // Outcome column
  ]
  ws['!cols'] = colWidths

  // Add worksheet to workbook
  XLSX.utils.book_append_sheet(wb, ws, 'Schedule')

  // Generate Excel file and trigger download
  XLSX.writeFile(wb, `schedule-${new Date().toISOString().split('T')[0]}.xlsx`)
}
</script>