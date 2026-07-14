<script setup lang="ts">
import { ref, onMounted } from 'vue'
import TheNavigation from './components/TheNavigation.vue'
import type { Stock3News } from './types/news'

const newsList = ref<Stock3News[]>([])

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

onMounted(() => {
  fetchNews()
})

</script>

<template>
  <TheNavigation />
  <main class="l-container">
    <h1 class="page-title">stock3 Live-Ticker</h1>
    <p>Geladene Meldungen: {{ newsList.length }}</p>
    </main>
</template>

<style lang="scss" scoped>
/* Das "l-" Präfix (Layout) ist eine Standard-BEM-Konvention für strukturelle Container */
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
</style>