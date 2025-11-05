<template>
  <div class="flex justify-center items-center space-x-3">
    <div class="relative w-full max-w-md">
      <i class="fa-solid fa-magnifying-glass absolute left-3 top-1/2 -translate-y-1/2 text-gray-400"></i>
      <input
        :value="modelValue"
        @input="$emit('update:modelValue', $event.target.value)"
        @keyup.enter="$emit('search')"
        placeholder="Search..."
        class="w-full p-3 pl-10 rounded-lg text-gray-900 bg-gray-100 focus:outline-none focus:ring-2 focus:ring-yellow-400 placeholder-gray-500 transition duration-200"
      />
    </div>
    <button
      @click="$emit('search')"
      class="bg-yellow-500 text-black px-5 py-2.5 rounded-lg font-bold hover:bg-yellow-400 transition duration-200 flex items-center gap-2 shadow-md hover:shadow-lg"
    >
      <i class="fa-solid fa-arrow-right"></i>
      Search
    </button>
  </div>
</template>

<script setup>
defineProps(['modelValue']);
defineEmits(['update:modelValue', 'search']);
</script>
