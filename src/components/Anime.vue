<template>
  <div
    v-if="item"
    class="relative group overflow-hidden rounded-2xl shadow-lg hover:scale-105 hover:shadow-yellow-400/20 transition-transform duration-300 cursor-pointer bg-gray-900"
  >
    <img
      :src="item.images?.jpg?.image_url || '/placeholder.jpg'"
      :alt="item.title || 'Unknown'"
      class="w-full h-80 object-cover group-hover:opacity-90 transition-opacity duration-300"
    />

    <div
      class="absolute inset-0 bg-gradient-to-t from-black/90 via-black/60 to-transparent opacity-0 group-hover:opacity-100 transition duration-300 p-4 flex flex-col justify-end"
    >
      <h2 class="text-lg font-bold mb-2 text-yellow-400">
        <i class="fa-solid fa-dragon mr-2"></i>{{ item.title || 'Untitled' }}
      </h2>

      <p class="text-sm text-gray-200 line-clamp-4">
        {{ item.synopsis || 'No synopsis available.' }}
      </p>

      <p class="text-xs mt-3 text-yellow-300 font-semibold flex items-center gap-1">
        <i class="fa-solid fa-star text-yellow-400"></i>
        Score: {{ item.score ?? 'N/A' }}
      </p>
    </div>
  </div>
</template>

<script setup>
defineProps({
  item: Object,
})
</script>
