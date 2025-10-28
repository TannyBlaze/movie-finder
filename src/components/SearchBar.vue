<template>
  <div class="flex justify-center">
    <input
      :value="modelValue"
      @input="$emit('update:modelValue', $event.target.value)"
      @keyup.enter="$emit('search')"
      placeholder="Search anime..."
      class="w-full max-w-md p-3 rounded-lg text-gray-900 focus:outline-none"
    />
    <button
      @click="$emit('search')"
      class="ml-3 bg-yellow-500 px-4 py-2 rounded-lg font-bold hover:bg-yellow-400"
    >
      Search
    </button>
  </div>
</template>

<script setup>
defineProps(['modelValue']);
defineEmits(['update:modelValue', 'search']);
</script>
