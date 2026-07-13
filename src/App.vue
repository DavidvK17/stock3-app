<script setup lang="ts">
import TheNavigation from './components/TheNavigation.vue'
</script>

<template>
  <TheNavigation />

  <main class="l-container">
    <h1 class="page-title">Marktübersicht</h1>
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