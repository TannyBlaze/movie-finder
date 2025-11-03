<template>
  <div
    v-if="item"
    class="relative group overflow-hidden rounded-2xl shadow-lg hover:scale-105 hover:shadow-emerald-400/20 transition-transform duration-300 cursor-pointer bg-gray-900"
  >
    <img
      :src="imageUrl"
      :alt="item.title || item.name || 'Unknown Web Novel'"
      class="w-full h-80 object-cover group-hover:opacity-90 transition-opacity duration-300"
    />

    <div
      class="absolute inset-0 bg-gradient-to-t from-black/90 via-black/60 to-transparent opacity-0 group-hover:opacity-100 transition duration-300 p-4 flex flex-col justify-end"
    >
      <h2 class="text-lg font-bold mb-2 text-emerald-400 flex items-center gap-2">
        <i class="fa-solid fa-book-open"></i>
        {{ item.title || item.name || 'Untitled Web Novel' }}
      </h2>

      <p class="text-sm text-gray-200 line-clamp-4">
        {{ item.synopsis || item.description || 'No description available.' }}
      </p>

      <p class="text-xs mt-3 text-emerald-300 font-semibold flex items-center gap-1">
        <i class="fa-solid fa-star text-yellow-400"></i>
        {{ item.score ?? item.rating ?? 'N/A' }}
      </p>
    </div>
  </div>
</template>

<script setup>
import { computed } from "vue";

const props = defineProps({
  item: Object,
});

const imageUrl = computed(() => {
  const item = props.item;
  return (
    item?.cover ||
    item?.image ||
    item?.images?.jpg?.image_url ||
    item?.image_url ||
    "https://placehold.co/600x800?text=No+Cover"
  );
});
</script>
