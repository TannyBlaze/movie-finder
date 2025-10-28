<template>
  <div
    v-if="item"
    class="relative group overflow-hidden rounded-lg shadow-lg hover:scale-105 transition duration-300 cursor-pointer"
  >
    <img
      :src="item.images?.jpg?.image_url || '/placeholder.jpg'"
      :alt="item.title || 'Unknown'"
      class="w-full h-80 object-cover"
    />
    <div
      class="absolute inset-0 bg-black/80 opacity-0 group-hover:opacity-100 transition duration-300 p-4 flex flex-col justify-end"
    >
      <h2 class="text-lg font-bold mb-2">{{ item.title || 'Untitled' }}</h2>
      <p class="text-sm line-clamp-4">
        {{ item.synopsis || 'No synopsis available.' }}
      </p>
      <p class="text-xs mt-2 text-yellow-400">
        Score: {{ item.score ?? 'N/A' }}
      </p>
    </div>
  </div>
</template>

<script setup>
defineProps({
  item: Object,
});
</script>
