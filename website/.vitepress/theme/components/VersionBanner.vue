<template>
  <div v-if="show" class="version-banner" role="note">
    <p>
      You are viewing the <strong>{{ banner.version }}</strong> documentation.
      <a :href="latestLink">
        View the latest release ({{ latest }})
      </a>
    </p>
  </div>
</template>

<script setup lang="ts">
import { computed, onMounted, ref } from 'vue'
import { useData } from 'vitepress'

const { theme } = useData()

const banner = computed(() => theme.value.versionBanner ?? {})

interface VersionManifest {
  latest?: string
  versions?: string[]
}

const manifest = ref<VersionManifest | null>(null)

onMounted(async () => {
  const root = banner.value.root
  if (!root) return
  try {
    const res = await fetch(`${root}versions.json`, { cache: 'no-store' })
    if (res.ok) {
      manifest.value = (await res.json()) as VersionManifest
    }
  } catch {
    // Manifest unreachable (e.g. local dev): keep the banner hidden.
  }
})

const latest = computed(() => manifest.value?.latest ?? '')

const show = computed(
  () =>
    !!banner.value.version &&
    banner.value.version !== 'main' &&
    !!latest.value &&
    latest.value !== banner.value.version,
)

const latestLink = computed(() => `${banner.value.root}${latest.value}/`)
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
