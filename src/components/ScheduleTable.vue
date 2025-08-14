<template>
  <div class="h-full">
    <div class="bg-white/80 backdrop-blur rounded-lg p-4 border border-gray-200/50 shadow-sm h-full">
      <h2 class="text-lg font-semibold text-gray-800 mb-4 flex items-center gap-2">
        <div class="w-2 h-2 bg-blue-500 rounded-full"></div>
        Schedule
      </h2>
      <div class="overflow-y-auto max-h-[600px] custom-scrollbar">
        <table class="w-full border-collapse">
          <thead class="sticky top-0 bg-white z-10">
            <tr>
              <th class="px-3 py-3 text-left text-xs font-semibold text-gray-600 uppercase tracking-wider border-b-2 border-gray-100">Time</th>
              <th class="px-3 py-3 text-left text-xs font-semibold text-gray-600 uppercase tracking-wider border-b-2 border-gray-100">Plan</th>
              <th class="px-3 py-3 text-left text-xs font-semibold text-gray-600 uppercase tracking-wider border-b-2 border-gray-100">Outcome</th>
            </tr>
          </thead>
          <tbody>
            <tr v-if="scheduleData.length === 0">
              <td colspan="3" class="text-center text-gray-500 italic py-12">
                <div class="flex flex-col items-center gap-2">
                  <div class="w-12 h-12 bg-gray-100 rounded-full flex items-center justify-center">
                  </div>
                  Generate your schedule to get started
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
</template>

<script setup>
import ScheduleRow from './ScheduleRow.vue'

defineProps({
  scheduleData: Array
})

const emit = defineEmits(['updateItem'])

const handleUpdate = (index, field, value) => {
  emit('updateItem', index, field, value)
}
</script>