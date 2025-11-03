<template>
  <div class="min-h-screen bg-gradient-to-b from-gray-900 to-black text-white px-4 sm:px-8 py-10">
    <h1
      class="text-4xl font-extrabold text-center mb-8 tracking-wide text-yellow-400 drop-shadow-lg"
    >
      <i class="fa-solid fa-clapperboard mr-2"></i>Media Mojo
    </h1>

    <div class="flex flex-wrap justify-center gap-3 mb-8">
      <button
        v-for="cat in categories"
        :key="cat"
        @click="selectCategory(cat)"
        :class="[
          'px-5 py-2.5 rounded-lg font-semibold transition-all duration-200 shadow-md focus:outline-none focus:ring-2 focus:ring-yellow-400/50',
          activeCategory === cat
            ? 'bg-yellow-400 text-black scale-105'
            : 'bg-gray-800 hover:bg-gray-700 text-gray-200 hover:scale-105',
        ]"
      >
        <i
          :class="{
            'fa-solid fa-film': cat === 'Movies',
            'fa-solid fa-tv': cat === 'Series',
            'fa-solid fa-dragon': cat === 'Anime',
            'fa-solid fa-book-open': cat === 'Manga',
            'fa-solid fa-scroll': cat === 'Web Novels',
            'fa-solid fa-gamepad': cat === 'Games',
          }"
          class="mr-2"
        ></i>
        {{ cat }}
      </button>
    </div>

    <div class="max-w-2xl mx-auto mb-8">
      <SearchBar v-model="searchQuery" @search="fetchContent" />
    </div>

    <div v-if="loading" class="flex justify-center mt-16 text-yellow-400 text-lg font-semibold">
      <i class="fa-solid fa-spinner fa-spin mr-2"></i>Loading...
    </div>

    <div
      v-if="!hasSearched && featuredList.length && !loading"
      class="mt-10"
    >
      <h2 class="text-2xl font-bold text-yellow-400 mb-4 text-center">
        <i class="fa-solid fa-fire mr-2"></i>Trending {{ activeCategory }}
      </h2>
      <div class="grid sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-6 mt-6 px-2 sm:px-4">
        <component
          :is="activeComponent"
          v-for="item in featuredList"
          :key="item.mal_id || item.id || item.slug"
          :item="item"
          class="hover:-translate-y-1 transition-transform duration-300"
        />
      </div>
    </div>

    <div
      v-if="contentList.length"
      class="grid sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-6 mt-8 px-2 sm:px-4"
    >
      <component
        :is="activeComponent"
        v-for="item in contentList"
        :key="item.mal_id || item.id || item.slug"
        :item="item"
        class="hover:-translate-y-1 transition-transform duration-300"
      />
    </div>

    <div
      v-else-if="!loading && hasSearched && !contentList.length"
      class="text-center mt-16 text-gray-400"
    >
      <i class="fa-solid fa-circle-exclamation text-yellow-400 text-2xl mb-3 block"></i>
      <p class="text-lg font-medium">No results found</p>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'
import axios from 'axios'
import SearchBar from './components/SearchBar.vue'
import Anime from './components/Anime.vue'
import Manga from './components/Manga.vue'
import Movie from './components/Movie.vue'
import Series from './components/Series.vue'
import Game from './components/Game.vue'
import WebNovel from './components/WebNovel.vue'

const TMDB_KEY = '3ee61c14232a3c0ab1187ad8b4b3df6c'
const RAWG_KEY = '3fdc90bcbab8422c8ed0fa8b61508af0'

const categories = ['Movies', 'Series', 'Anime', 'Manga', 'Web Novels', 'Games']
const activeCategory = ref('Movies')
const searchQuery = ref('')
const contentList = ref([])
const featuredList = ref([])
const loading = ref(false)
const hasSearched = ref(false)

const categoryMap = {
  Anime,
  Manga,
  Movies: Movie,
  Series,
  Games: Game,
  'Web Novels': WebNovel,
}

const activeComponent = computed(() => categoryMap[activeCategory.value])

const selectCategory = async (cat) => {
  activeCategory.value = cat
  contentList.value = []
  searchQuery.value = ''
  hasSearched.value = false
  await loadFeaturedContent()
}

const fetchContent = async () => {
  if (!searchQuery.value.trim()) return
  loading.value = true
  hasSearched.value = true

  try {
    let res

    switch (activeCategory.value) {
      case 'Anime':
        res = await axios.get(`https://api.jikan.moe/v4/anime?q=${searchQuery.value}&limit=20`)
        contentList.value = res.data.data || []
        break

      case 'Manga':
        res = await axios.get(`https://api.jikan.moe/v4/manga?q=${searchQuery.value}&limit=20`)
        contentList.value = res.data.data || []
        break

      case 'Movies':
        res = await axios.get(
          `https://api.themoviedb.org/3/search/movie?api_key=${TMDB_KEY}&query=${searchQuery.value}`,
        )
        contentList.value = res.data.results || []
        break

      case 'Series':
        res = await axios.get(
          `https://api.themoviedb.org/3/search/tv?api_key=${TMDB_KEY}&query=${searchQuery.value}`,
        )
        contentList.value = res.data.results || []
        break

      case 'Games':
        res = await axios.get(
          `https://api.rawg.io/api/games?key=${RAWG_KEY}&search=${searchQuery.value}`,
        )
        contentList.value = res.data.results || []
        break

      case 'Web Novels':
        try {
          const response = await axios.get(
            `https://api.allorigins.win/raw?url=${encodeURIComponent(
              `https://fnovels.1ani.me/extra/api/search?query=${searchQuery.value}`,
            )}`,
          )
          if (typeof response.data === 'string') {
            console.warn('Unexpected string response — switching to fallback API')
            throw new Error('Invalid JSON')
          }
          contentList.value = response.data.results || response.data || []
        } catch (error) {
          console.error('Web Novel fetch failed, using fallback:', error)
          const fallback = await axios.get(
            `https://api.jikan.moe/v4/manga?q=${searchQuery.value}&genres_exclude=12&limit=20`,
          )
          contentList.value = fallback.data.data || []
        }
        break
    }
  } catch (err) {
    console.error('Error fetching content:', err)
  } finally {
    loading.value = false
  }
}

const loadFeaturedContent = async () => {
  try {
    let res
    switch (activeCategory.value) {
      case 'Movies':
        res = await axios.get(`https://api.themoviedb.org/3/trending/movie/week?api_key=${TMDB_KEY}`)
        featuredList.value = res.data.results.slice(0, 8)
        break
      case 'Series':
        res = await axios.get(`https://api.themoviedb.org/3/trending/tv/week?api_key=${TMDB_KEY}`)
        featuredList.value = res.data.results.slice(0, 8)
        break
      case 'Anime':
        res = await axios.get(`https://api.jikan.moe/v4/top/anime?limit=8`)
        featuredList.value = res.data.data
        break
      case 'Games':
        res = await axios.get(`https://api.rawg.io/api/games?key=${RAWG_KEY}&ordering=-rating&page_size=8`)
        featuredList.value = res.data.results
        break
      case 'Manga':
        res = await axios.get(`https://api.jikan.moe/v4/top/manga?limit=8`)
        featuredList.value = res.data.data
        break
      case 'Web Novels':
        res = await axios.get(`https://api.jikan.moe/v4/manga?q=novel&limit=8`)
        featuredList.value = res.data.data
        break
    }
  } catch (err) {
    console.error('Error loading featured content:', err)
  }
}

onMounted(loadFeaturedContent)
</script>

<style>
body {
  margin: 0;
  font-family: 'Inter', sans-serif;
}
</style>
