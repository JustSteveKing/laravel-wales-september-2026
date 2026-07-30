<script setup lang="ts">
type Tone = 'run' | 'signal' | 'human' | 'sleep' | 'plain'

withDefaults(defineProps<{
  value: string
  label?: string
  sub?: string
  tone?: Tone
}>(), {
  tone: 'plain',
})

// Sourced from the theme so a palette change is a one-file change.
const palette: Record<Tone, string> = {
  run: 'var(--wf-run)',
  signal: 'var(--wf-signal)',
  human: 'var(--wf-human)',
  sleep: 'var(--wf-sleep)',
  plain: 'var(--jsk-heading)',
}
</script>

<template>
  <div class="flex flex-col gap-1">
    <div
      class="text-5xl font-bold leading-none tabular-nums"
      :style="{ color: palette[tone], fontFamily: 'var(--jsk-font-sans)', letterSpacing: '-0.03em' }"
    >
      {{ value }}
    </div>
    <div
      v-if="label"
      class="text-xs font-semibold uppercase"
      :style="{ fontFamily: 'var(--jsk-font-sans)', letterSpacing: '0.08em', color: 'var(--jsk-muted)' }"
    >
      {{ label }}
    </div>
    <div v-if="sub" class="text-xs leading-snug" :style="{ color: 'var(--jsk-muted)', opacity: 0.7 }">
      {{ sub }}
    </div>
  </div>
</template>
