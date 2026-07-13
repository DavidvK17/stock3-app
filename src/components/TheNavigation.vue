<script setup lang="ts">
import { ref } from 'vue'

const isSubmenuOpen = ref(false)

function toggleSubmenu() {
  isSubmenuOpen.value = !isSubmenuOpen.value
}
</script>

<template>
  <header class="header">
    
    <nav class="header__nav">
      <ul class="nav-list">
        <li class="nav-list__item"><a href="#" class="nav-list__link">News</a></li>
        <li class="nav-list__item"><a href="#" class="nav-list__link">Märkte</a></li>
        
        <li class="nav-list__item">
          <button 
            class="nav-list__btn" 
            :class="{ 'nav-list__btn--active': isSubmenuOpen }"
            @click="toggleSubmenu">
            Analysen
            <span class="nav-list__icon">{{ isSubmenuOpen ? '▲' : '▼' }}</span>
          </button>
        </li>
      </ul>
    </nav>

    <div class="header__submenu" v-if="isSubmenuOpen">
      <ul class="nav-list nav-list--secondary">
        <li class="nav-list__item"><a href="#" class="nav-list__link">Charttechnik</a></li>
        <li class="nav-list__item"><a href="#" class="nav-list__link">Fundamentaldaten</a></li>
        <li class="nav-list__item"><a href="#" class="nav-list__link">Experten-Meinungen</a></li>
      </ul>
    </div>
  </header>
</template>

<style lang="scss" scoped>
/* Durch "scoped" kompiliert Vue das zu einzigartigen Klassen (z.B. .header[data-v-f3f3eg9]) */

.header {
  background-color: var(--c-bg-surface);
  border-bottom: 1px solid var(--c-bg-elevated);

  &__nav { // SCSS Nesting für BEM (.header__nav)
    padding: calc(var(--spacing-base) * 2) calc(var(--spacing-base) * 3);
  }

  &__submenu {
    background-color: var(--c-bg-elevated);
    padding: calc(var(--spacing-base) * 1.5) calc(var(--spacing-base) * 3);
    border-top: 1px solid rgba(255,255,255, 0.05);
  }
}

.nav-list {
  display: flex;
  align-items: center;
  list-style: none;
  gap: calc(var(--spacing-base) * 3);

  &--secondary { // BEM Modifier für das Untermenü (.nav-list--secondary)
    gap: calc(var(--spacing-base) * 2);
    
    .nav-list__link {
      color: var(--c-text-muted);
      font-size: 0.9rem;
      
      &:hover {
        color: var(--c-text-main);
      }
    }
  }

  &__link, &__btn {
    color: var(--c-text-main);
    text-decoration: none;
    font-size: 1rem;
    font-weight: 500;
    transition: color 0.2s ease;
  }

  &__btn {
    background: transparent;
    border: none;
    cursor: pointer;
    display: flex;
    align-items: center;
    gap: calc(var(--spacing-base) * 0.5);
    font-family: inherit;

    /* HIER GEÄNDERT: stock3 Primary Blue beim Hover & Active */
    &:hover, &--active {
      color: var(--c-brand-primary);
    }
  }

  &__icon {
    font-size: 0.7rem;
  }
}
</style>