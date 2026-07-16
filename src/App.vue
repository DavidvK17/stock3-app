<script setup lang="ts">
import { ref, onMounted, computed } from 'vue'
import type { Stock3News } from './types/news'
import TheNavigation from './components/TheNavigation.vue'
import NewsCard from './components/NewsCard.vue'
import NewsModal from './components/NewsModal.vue'

const newsList = ref<Stock3News[]>([])
const selectedNews = ref<Stock3News | null>(null)
const searchQuery = ref('')
const selectedTag = ref<string | null>(null)

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

    let matchesSearch = false
    if (!query) {
      matchesSearch = true
    } else {
      matchesSearch = item.headline.toLowerCase().includes(query) || item.summary.toLowerCase().includes(query)
    }

    let matchesTag = false
    if (!selectedTag.value) {
      matchesTag = true
    } else {
      matchesTag = item.tags.includes(selectedTag.value)
    }

    return matchesSearch && matchesTag
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

const toggleTag = (tag: string) => {
  if (selectedTag.value === tag) {
    selectedTag.value = null
  } else {
    selectedTag.value = tag
  }
}

const resetFilters = () => {
  searchQuery.value = ''
  selectedTag.value = null
}

onMounted(() => {
  fetchNews()
})

</script>

<template>
  <TheNavigation />
  <main class="l-container">
    <header>
      <h1 class="page-title">stock3 News-Feed</h1>

      <div class="search-container">
        <input 
        v-model="searchQuery"
        type="search"
        placeholder="Nach News suchen (z.B. Dax, Nvidia...)"
        class="search-input" 
        />
      </div>

      <div class="filter-status">
        <p class="news-count">{{ filteredAndSortedNewsList.length }} Meldungen:</p>
        <span v-if="selectedTag" class="active-badge">
          Tag: #{{ selectedTag }}
          <button class="active-badge__clear" @click="selectedTag = null">&times;</button>
        </span>
        <button 
        v-if="selectedNews || searchQuery"
        class="clear-filters-btn"
        @click="resetFilters"
        >
          Filter zurücksetzen
        </button>
      </div>
    </header>

    <section class="news-grid">
      <NewsCard
        v-for="item in filteredAndSortedNewsList"
        :key="item.id"
        :news="item"
        @select="openNews"
        @tag-click="toggleTag"
      />
    </section>

    <div v-if="filteredAndSortedNewsList.length === 0" class="no-results">
      <span class="no-results__icon">🔍</span>
      <p>Keine Meldungen für deine Auswahl gefunden.</p>
      <button class="no-results__btn" @click="resetFilters">Alle Filter löschen.</button>
    </div>

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

.ticker-header {
  margin-bottom: calc(var(--spacing-base) * 4);
}

.page-title {
  font-size: 2rem;
  margin-bottom: calc(var(--spacing-base) * 3);
  color: var(--c-brand-primary);
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
    border-color: var(--c-brand-primary);
    box-shadow: 0 0 0 3px rgba(0, 102, 204, 0.15);
  }
}

.filter-status {
  display: flex;
  align-items: center;
  gap: calc(var(--spacing-base) * 2);
  flex-wrap: wrap;
  font-size: 0.9rem;
  margin-bottom: calc(var(--spacing-base) * 3);
}

.news-count {
  color: #666;
  margin: 0;
}

.active-badge {
  background-color: var(--c-brand-primary);
  color: #ffffff;
  padding: 4px 10px;
  border-radius: 20px;
  font-weight: 500;
  font-size: 0.8rem;
  display: inline-flex;
  align-items: center;
  gap: 6px;

  &__clear {
    background: none;
    border: none;
    color: #ffffff;
    font-size: 1.1rem;
    cursor: pointer;
    line-height: 1;
    padding: 0;
    display: flex;
    align-items: center;
    justify-content: center;
    opacity: 0.8;

    &:hover {
      opacity: 1;
    }
  }
}

.clear-filters-btn {
  background: none;
  border: none;
  color: #c0392b;
  cursor: pointer;
  font-size: 0.85rem;
  text-decoration: underline;
  padding: 0;

  &:hover {
    color: #962d22;
  }
}

.no-results {
  text-align: center;
  padding: 40px;
  background: #fdfdfd;
  border: 1px dashed #ccc;
  border-radius: 8px;
  margin-top: 20px;

  &__icon {
    font-size: 2.5rem;
    display: block;
    margin-bottom: 10px;
  }

  p {
    color: #555;
    margin-bottom: 15px;
  }

  &__btn {
    background-color: var(--c-brand-primary);
    color: #fff;
    border: none;
    padding: 8px 16px;
    border-radius: 6px;
    cursor: pointer;
    font-size: 0.9rem;

    &:hover {
      background-color: #0052a3;
    }
  }
}

.news-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(320px, 1fr));
  gap: calc(var(--spacing-base) * 3);
}
</style>