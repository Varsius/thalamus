<template>
  <div v-if="show" class="version-banner" role="note">
    <p>
      You are viewing the <strong>{{ banner.version }}</strong> documentation.
      <a :href="banner.latestLink">
        View the latest release ({{ banner.latestVersion }})
      </a>
    </p>
  </div>
</template>

<script setup lang="ts">
import { computed } from 'vue'
import { useData } from 'vitepress'

const { theme } = useData()

const banner = computed(() => theme.value.versionBanner ?? {})

const show = computed(
  () =>
    !!banner.value.version &&
    !!banner.value.latestVersion &&
    !!banner.value.latestLink &&
    banner.value.version !== 'main' &&
    banner.value.version !== banner.value.latestVersion,
)
</script>

<style scoped>
.version-banner {
  border-bottom: 1px solid var(--vp-c-divider);
  background-color: var(--vp-c-bg-soft);
  padding: 8px 16px;
  text-align: center;
  font-size: 13px;
  line-height: 1.5;
  color: var(--vp-c-text-2);
}

.version-banner p {
  margin: 0;
}

.version-banner a {
  color: var(--vp-c-brand-1);
  text-decoration: none;
}

.version-banner a:hover {
  text-decoration: underline;
}
</style>
