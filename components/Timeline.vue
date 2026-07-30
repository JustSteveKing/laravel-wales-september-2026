<script setup lang="ts">
import { onSlideEnter, onSlideLeave, useNav, useSlideContext } from '@slidev/client'
import { computed, onMounted, onUnmounted, ref, shallowRef } from 'vue'

type Kind = 'run' | 'signal' | 'human' | 'sleep'

interface Segment {
  label: string
  kind: Kind
  seconds: number
  note?: string
}

// Order #4471. The one real example the whole talk hangs off.
const ORDER_4471: Segment[] = [
  { label: 'Reserve stock', kind: 'run', seconds: 0.12 },
  { label: 'Authorise payment', kind: 'run', seconds: 0.21 },
  { label: 'Stripe webhook', kind: 'signal', seconds: 4, note: 'await payment_captured' },
  { label: 'Risk gate', kind: 'run', seconds: 0.04 },
  { label: 'Human review', kind: 'human', seconds: 22860, note: 'await review_decision' },
  { label: 'Decide + pack', kind: 'run', seconds: 0.22 },
  { label: 'In transit', kind: 'sleep', seconds: 172800, note: 'sleep(48h)' },
  { label: 'Follow-up email', kind: 'run', seconds: 0.3 },
]

const props = withDefaults(defineProps<{
  segments?: Segment[]
  /** `linear` is honest and unreadable. `log` is readable and says so. */
  scale?: 'linear' | 'log'
  labels?: boolean
  notes?: boolean
  height?: string
  /**
   * How the segments arrive.
   * `none`  — all at once, the way the deck has always drawn it.
   * `auto`  — they wipe in left to right on slide enter, `stagger` ms apart.
   * `click` — one segment per click, so you can talk over each stop.
   */
  reveal?: 'none' | 'auto' | 'click'
  /** Gap between segments in `auto` mode, in ms. */
  stagger?: number
  /** Which click the first segment lands on, in `click` mode. Slidev `at` syntax. */
  at?: number | string
}>(), {
  scale: 'linear',
  labels: false,
  notes: false,
  height: '4.5rem',
  reveal: 'none',
  stagger: 110,
  at: '+1',
})

// The default lives here, not in withDefaults: a default factory is hoisted out of
// setup() and so cannot reference ORDER_4471 above it.
const items = computed(() => props.segments ?? ORDER_4471)

// Colours live in the theme (theme/styles/tokens.css) so the legend, these bars and
// the inline spans in slides.md can never drift apart.
function ink(kind: Kind): string {
  return `var(--wf-${kind})`
}

const weights = computed(() => items.value.map(s =>
  props.scale === 'log'
    ? Math.max(0.4, Math.log10(Math.max(s.seconds, 0.001)) + 3)
    : s.seconds,
))

const total = computed(() => weights.value.reduce((a, b) => a + b, 0))

function width(index: number): string {
  return `${(weights.value[index] / total.value) * 100}%`
}

// Solid means work is happening. Hatched means nothing is.
function fill(kind: Kind): string {
  return kind === 'run'
    ? `var(--wf-run)`
    : `repeating-linear-gradient(135deg, var(--wf-${kind}-soft) 0 5px, var(--wf-${kind}-faint) 5px 10px)`
}

function duration(seconds: number): string {
  if (seconds < 1)
    return `${Math.round(seconds * 1000)}ms`
  if (seconds < 60)
    return `${+seconds.toFixed(1)}s`
  if (seconds < 3600)
    return `${Math.round(seconds / 60)}m`
  if (seconds < 86400) {
    const h = Math.floor(seconds / 3600)
    const m = Math.round((seconds % 3600) / 60)
    return m ? `${h}h ${m}m` : `${h}h`
  }
  return `${+(seconds / 86400).toFixed(1)}d`
}

// ---- Reveal ---------------------------------------------------------------

const root = ref<HTMLElement>()
const { $clicks, $clicksContext, $renderContext } = useSlideContext()
const { isPrintMode, isPrintWithClicks } = useNav()

// In click mode the component claims one click per segment, exactly as a stack of
// `v-click` elements would. Registering in onMounted (not setup) is what keeps our
// slot in the queue in document order relative to any `v-click` on the same slide.
const clickInfo = shallowRef<ReturnType<typeof $clicksContext.calculateSince>>(null)

onMounted(() => {
  if (props.reveal !== 'click' || !root.value)
    return
  clickInfo.value = $clicksContext.calculateSince(props.at, items.value.length)
  $clicksContext.register(root.value, clickInfo.value)
})

onUnmounted(() => {
  if (root.value)
    $clicksContext.unregister(root.value)
})

// `auto` needs a painted frame in the hidden state before it flips, or the browser
// coalesces both states into one and there is no transition to see.
const started = ref(false)
onSlideEnter(() => {
  if (props.reveal === 'auto')
    requestAnimationFrame(() => requestAnimationFrame(() => started.value = true))
})
onSlideLeave(() => {
  if (props.reveal === 'auto')
    started.value = false
})

// The overview grid, the presenter's next-slide preview and PDF export all want the
// finished picture, not a frozen frame halfway through it. `export --with-clicks` is
// the exception: it wants one page per step, so leave the click reveal alone there.
const isStatic = computed(() => {
  if (!['slide', 'presenter'].includes($renderContext.value))
    return true
  return isPrintMode.value && !(props.reveal === 'click' && isPrintWithClicks.value)
})

const revealed = computed(() => {
  if (props.reveal === 'none' || isStatic.value)
    return items.value.length
  if (props.reveal === 'click')
    return clickInfo.value ? $clicks.value - clickInfo.value.start + 1 : 0
  return started.value ? items.value.length : 0
})

function shown(index: number): boolean {
  return index < revealed.value
}

// Only the cascade is delayed. Hiding again (slide leave) snaps back at once.
function delay(index: number): string {
  return shown(index) && props.reveal === 'auto' ? `${index * props.stagger}ms` : '0ms'
}
</script>

<template>
  <div ref="root" class="w-full">
    <div v-if="labels" class="flex gap-[2px] items-end mb-2 text-[10px] leading-tight">
      <div
        v-for="(segment, i) in items"
        :key="`l-${segment.label}`"
        :style="{
          width: width(i),
          color: ink(segment.kind),
          fontFamily: 'var(--jsk-font-sans)',
          transitionDelay: delay(i),
        }"
        class="tl-text min-w-0 truncate font-medium"
        :class="{ 'is-pending': !shown(i) }"
      >
        {{ segment.label }}
      </div>
    </div>

    <div class="flex gap-[2px]" :style="{ height }">
      <div
        v-for="(segment, i) in items"
        :key="segment.label"
        :style="{
          width: width(i),
          minWidth: '3px',
          background: fill(segment.kind),
          boxShadow: `inset 0 0 0 1px var(--wf-${segment.kind}-soft)`,
          transitionDelay: delay(i),
        }"
        class="tl-bar rounded-sm"
        :class="{ 'is-pending': !shown(i) }"
      />
    </div>

    <div
      v-if="labels"
      class="flex gap-[2px] mt-2 text-[10px] opacity-60"
      :style="{ fontFamily: 'var(--jsk-font-mono)' }"
    >
      <div
        v-for="(segment, i) in items"
        :key="`d-${segment.label}`"
        :style="{ width: width(i), transitionDelay: delay(i) }"
        class="tl-text min-w-0 truncate"
        :class="{ 'is-pending': !shown(i) }"
      >
        {{ duration(segment.seconds) }}
      </div>
    </div>

    <div
      v-if="notes"
      class="flex gap-[2px] mt-1 text-[10px] leading-tight"
      :style="{ fontFamily: 'var(--jsk-font-mono)' }"
    >
      <div
        v-for="(segment, i) in items"
        :key="`n-${segment.label}`"
        :style="{ width: width(i), color: ink(segment.kind), transitionDelay: delay(i) }"
        class="tl-text min-w-0"
        :class="{ 'is-pending': !shown(i) }"
      >
        {{ segment.note ?? '' }}
      </div>
    </div>
  </div>
</template>

<style scoped>
/* Bars wipe in from the left, so a staggered run reads as the timeline drawing
   itself rather than eight things popping. Transform only — the track keeps its
   geometry, so nothing reflows as segments arrive. */
.tl-bar {
  transform-origin: left center;
  transition:
    transform 460ms cubic-bezier(0.22, 1, 0.36, 1),
    opacity 220ms ease-out;
}

.tl-bar.is-pending {
  transform: scaleX(0);
  opacity: 0;
}

/* Labels can't be scaled without squashing the type, so they lift instead. */
.tl-text {
  transition:
    opacity 320ms ease-out,
    transform 320ms cubic-bezier(0.22, 1, 0.36, 1);
}

.tl-text.is-pending {
  opacity: 0;
  transform: translateY(4px);
}

@media (prefers-reduced-motion: reduce) {
  .tl-bar,
  .tl-text {
    transition-duration: 1ms;
    transition-delay: 0ms !important;
  }

  .tl-bar.is-pending {
    transform: none;
  }

  .tl-text.is-pending {
    transform: none;
  }
}
</style>
