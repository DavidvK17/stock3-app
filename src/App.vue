<script setup lang="ts">
import { ref, onMounted, computed } from 'vue'
import type { Stock3News } from './types/news'
import TheNavigation from './components/TheNavigation.vue'
import NewsCard from './components/NewsCard.vue'
import NewsModal from './components/NewsModal.vue'

const newsList = ref<Stock3News[]>([])
const selectedNews = ref<Stock3News | null>(null)
const searchQuery = ref('')

async function fetchNews() {
  try {
    const response = await fetch('/news.json')
    if (!response.ok) {
      throw new Error('Netzwerk-Antwort war nicht ok')
    }
    const data: Stock3News[] = await response.json()
    newsList.value = data
  } catch (error) {
    console.error('Fehler beim Laden der News:', error)
  }
}

const filteredAndSortedNewsList = computed(() => {
  const filtered = newsList.value.filter((item) => {
    const query = searchQuery.value.toLowerCase().trim()
    if (!query) return true
    const matchesHeadline = item.headline.toLowerCase().includes(query)
    const matchesSummary = item.headline.toLowerCase().includes(query)
    return matchesHeadline || matchesSummary
  })
  return filtered.sort((a, b) => {
    return new Date(b.timestamp).getTime() - new Date(a.timestamp).getTime()
  })
})

const openNews = (news: Stock3News) => {
  selectedNews.value = news
}

const closeNews = () => {
  selectedNews.value = null
}

onMounted(() => {
  fetchNews()
})

</script>

<template>
  <TheNavigation />
  <main class="l-container">
    <header>
      <h1 class="page-title">stock3 Live-Ticker</h1>
      <div class="search-container">
        <input 
        v-model="searchQuery"
        type="text"
        placeholder="Nach News suchen (z.B. Dax, Nvidia...)"
        class="search-input" 
        />
      </div>
      <p class="news-count">{{ filteredAndSortedNewsList.length }} Meldungen:</p>
    </header> 
    <section class="news-grid">
      <NewsCard
        v-for="item in filteredAndSortedNewsList"
        :key="item.id"
        :news="item"
        @select="openNews"
      />
    </section>

    <NewsModal
      v-if="selectedNews"
      :news="selectedNews"
      @close="closeNews"
    />
    </main>
</template>

<style lang="scss" scoped>
.l-container {
  max-width: 1200px;
  margin: 0 auto;
  padding: calc(var(--spacing-base) * 4);
}

.page-title {
  font-size: 2rem;
  margin-bottom: calc(var(--spacing-base) * 3);
  color: var(--c-brand-primary);
}

.news-count {
  font-size: 0.9rem;
  margin-bottom: calc(var(--spacing-base) * 3);
  color: var(--c-brand-secondary);
}

.search-container {
  margin: calc(var(--spacing-base) * 3) 0;
}

.search-input {
  width: 100%;
  max-width: 400px;
  padding: 10px 16px;
  font-size: 1rem;
  border: 1px solid #ccc;
  border-radius: 6px;
  outline: none;
  transition: border-color 0.2s ease, box-shadow 0.2s ease;

  &:focus {
    border-color: var(--c-brand-primary); /* Unser stock3-Blau */
    box-shadow: 0 0 0 3px rgba(0, 102, 204, 0.15);
  }
}

/* Flexibles CSS Grid für das Layout */
.news-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(320px, 1fr));
  gap: calc(var(--spacing-base) * 3);
}
</style>