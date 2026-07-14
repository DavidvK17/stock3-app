<script setup lang="ts">
import { ref, onMounted, computed } from 'vue'
import type { Stock3News } from './types/news'
import TheNavigation from './components/TheNavigation.vue'
import NewsCard from './components/NewsCard.vue'
import NewsModal from './components/NewsModal.vue'

const newsList = ref<Stock3News[]>([])
const selectedNews = ref<Stock3News | null>(null)

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

const sortedNewsList = computed(() => {
  return [...newsList.value].sort((a, b) => {
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
      <p>{{ newsList.length }} Meldungen:</p>
    </header> 
    <section class="news-grid">
      <NewsCard
        v-for="item in sortedNewsList"
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
  color: #666;
  background: #f5f5f5;
  padding: 4px 12px;
  border-radius: 20px;
}

/* Flexibles CSS Grid für das Layout */
.news-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(320px, 1fr));
  gap: calc(var(--spacing-base) * 3);
}
</style>