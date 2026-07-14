<script setup lang="ts">
import type { Stock3News } from '../types/news'

defineProps<{
    news: Stock3News
}>()

const emit = defineEmits<{
    close: []
}>()
</script>

<template>
    <div class="modal-backdrop" @click="emit('close')">
        <div class="modal-content" @click.stop>
            <header class="modal-header">
                <span class="modal-author">Veröffentlicht von: {{ news.author }}</span>
                <button class="modal-close" @click="emit('close')">&times;</button>
            </header>

            <h2 class="modal-title">{{ news.headline }}</h2>

            <div class="modal-body">
                <p class="modal-text">{{ news.content }}</p>
            </div>
        </div>
    </div>
</template>

<style lang="scss" scoped>
.modal-backdrop {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background-color: rgba(0, 0, 0, 0.6);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

.modal-content {
  background: #ffffff;
  padding: calc(var(--spacing-base) * 5);
  border-radius: 12px;
  max-width: 650px;
  width: 90%;
  max-height: 80vh;
  overflow-y: auto;
  box-shadow: 0 15px 30px rgba(0, 0, 0, 0.25);
}

.modal-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: calc(var(--spacing-base) * 3);
}

.modal-author {
  font-size: 0.85rem;
  color: #666;
  font-weight: 500;
}

.modal-close {
  background: none;
  border: none;
  font-size: 2rem;
  line-height: 1;
  cursor: pointer;
  color: #999;
  transition: color 0.2s ease;
  
  &:hover {
    color: #333;
  }
}

.modal-title {
  font-size: 1.6rem;
  line-height: 1.3;
  margin: 0 0 calc(var(--spacing-base) * 3) 0;
  color: var(--c-brand-primary);
}

.modal-text {
  font-size: 1.05rem;
  line-height: 1.6;
  color: #333;
  white-space: pre-line;
}
</style>