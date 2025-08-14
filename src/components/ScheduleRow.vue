<template>
  <tr class="group hover:bg-blue-50/50 transition-all duration-200">
    <td class="px-3 py-4 border-b border-gray-100">
      <div class="font-mono text-sm text-gray-600 bg-gray-50 px-2 py-1 rounded-md inline-block">
        {{ item.startTime }} - {{ item.endTime }}
      </div>
    </td>
    <td class="px-3 py-4 border-b border-gray-100">
      <input
        type="text"
        :value="item.plan"
        @input="updateField('plan', $event.target.value)"
        placeholder="What's your plan?"
        class="w-full px-3 py-2 border border-gray-200 rounded-lg text-sm focus:outline-none focus:border-blue-400 focus:ring-2 focus:ring-blue-100 transition-all bg-white/80 backdrop-blur"
      >
    </td>
    <td class="px-3 py-4 border-b border-gray-100">
      <input
        type="text"
        :value="item.outcome"
        @input="updateField('outcome', $event.target.value)"
        placeholder="What actually happened?"
        class="w-full px-3 py-2 border border-gray-200 rounded-lg text-sm focus:outline-none focus:border-green-400 focus:ring-2 focus:ring-green-100 transition-all bg-white/80 backdrop-blur"
      >
    </td>
  </tr>
</template>

<script setup>
const props = defineProps({
  item: Object,
  index: Number
})

const emit = defineEmits(['update'])

const updateField = (field, value) => {
  emit('update', props.index, field, value)
}
</script>