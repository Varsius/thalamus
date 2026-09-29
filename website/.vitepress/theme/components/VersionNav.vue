<template>
  <div v-if="items.length" class="VersionNav VPFlyout" :class="{ open }">
    <button
      class="button"
      type="button"
      :aria-expanded="open"
      @click="open = !open"
      @mouseenter="open = true"
      @focus="open = true"
    >
      <span class="text">{{ label }}</span>
    </button>
    <ul class="menu" @mouseleave="open = false">
      <li v-for="item in items" :key="item.value" class="item">
        <a class="vp-raw" :href="link(item.value)">
          <span class="text">{{ item.text }}</span>
        </a>
      </li>
    </ul>
  </div>
</template>

<script setup lang="ts">
import { computed, ref } from 'vue'
import { useVersions } from '../composables/useVersions'

const props = defineProps<{
  version: string
  root: string
  baseUrl?: string
}>()

const { versions } = useVersions(props.root)

const open = ref(false)

const label = computed(
  () => (props.version === 'main' ? 'main (dev)' : props.version || 'version'),
)

const items = computed(() => {
  const list = versions.value.map((v) => ({ text: v, value: v }))
  if (props.version !== 'main') {
    list.push({ text: 'main (dev)', value: 'main' })
  }
  return list
})

const link = (v: string) =>
  props.baseUrl ? `${props.baseUrl}${props.root}${v}/` : `${props.root}${v}/`
</script>

<style scoped>
.VersionNav {
  position: relative;
}

.button {
  display: flex;
  align-items: center;
  padding: 0 12px;
  height: var(--vp-nav-height);
  border: 0;
  border-radius: var(--vp-button-border-radius);
  background: transparent;
  font-size: var(--vp-font-size-sm);
  font-weight: 500;
  color: var(--vp-c-text-1);
  cursor: pointer;
  transition: color 0.25s;
}

.VersionNav:hover .button,
.VersionNav.open .button {
  color: var(--vp-c-brand-1);
}

.text {
  line-height: var(--vp-nav-height);
}

.menu {
  position: absolute;
  top: calc(var(--vp-nav-height) / 2 + 20px);
  right: 0;
  min-width: 150px;
  margin: 0;
  padding: 8px;
  list-style: none;
  background-color: var(--vp-c-bg-soft);
  border: 1px solid var(--vp-c-divider);
  border-radius: var(--vp-button-border-radius);
  box-shadow: var(--vp-shadow-2);
  opacity: 0;
  visibility: hidden;
  transform: translateY(0);
  transition: opacity 0.25s, visibility 0.25s, transform 0.25s;
}

.VersionNav:hover .menu,
.VersionNav.open .menu {
  opacity: 1;
  visibility: visible;
}

.item a {
  display: flex;
  align-items: center;
  height: 26px;
  padding: 0 8px;
  border-radius: 6px;
  font-size: var(--vp-font-size-sm);
  font-weight: 500;
  color: var(--vp-c-text-1);
  text-decoration: none;
}

.item a:hover {
  color: var(--vp-c-brand-1);
}
</style>
