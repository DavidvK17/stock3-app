<script setup lang="ts">
import type { Stock3News } from '../types/news'

defineProps<{
    news: Stock3News
}>()

const emit = defineEmits<{
  'select': [news: Stock3News]
  'tag-click': [tag: string]
}>()

const formatTime = (isoString: string) => {
    const date = new Date(isoString)
    return date.toLocaleTimeString('de-DE', { hour: '2-digit', minute: '2-digit'})
}
</script>

<template>
    <article class="news-card" @click="emit('select', news)">
        <div class="news-card__meta">
            <time class="news-card__time" :datetime="news.timestamp">
                {{ formatTime(news.timestamp) }}
            </time>
            <span class="news-card__author">{{ news.author }}</span>
        </div>

        <h2 class="news-card__headline">{{ news.headline }}</h2>
        <p class="news-card__summary">{{ news.summary }}</p>

        <div class="news-card__tags">
            <span 
            v-for="tag in news.tags"
            :key="tag"
            class="news-card__tag"
            @click.stop="emit('tag-click', tag)"
            >
            {{ tag }}
        </span>
        </div>
    </article>
</template>

<style lang="scss" scoped>
.news-card {
  background-color: #ffffff;
  border: 1px solid #e0e0e0;
  border-radius: 8px;
  padding: calc(var(--spacing-base) * 3);
  transition: box-shadow 0.2s ease, transform 0.2s ease;
  cursor: pointer;

  &:hover {
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.08);
    transform: translateY(-2px);
  }

  &__meta {
    display: flex;
    justify-content: space-between;
    font-size: 0.85rem;
    color: #666;
    margin-bottom: calc(var(--spacing-base) * 1.5);
  }

  &__time {
    font-weight: bold;
    color: var(--c-brand-primary);
  }

  &__headline {
    font-size: 1.1rem;
    margin: 0 0 calc(var(--spacing-base) * 1.5) 0;
    line-height: 1.4;
    color: #333;
  }

  &__summary {
    font-size: 0.95rem;
    color: #555;
    margin: 0 0 calc(var(--spacing-base) * 2) 0;
    line-height: 1.5;
  }

  &__tags {
    display: flex;
    gap: calc(var(--spacing-base) * 1);
    flex-wrap: wrap;
  }

  &__tag {
    font-size: 0.75rem;
    background-color: #f0f0f0;
    padding: 2px 8px;
    border-radius: 4px;
    color: #444;
  }
}
</style>