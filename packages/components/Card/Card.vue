<script setup lang="ts">
import type { CardProps } from './types'
import { onMounted } from 'vue'
import { debugWarn } from '@pixel-ui/utils'

import workletURL from '../worklets/dist/pixelbox.worklet.js?url'

const COMP_NAME = 'PxCard' as const
defineOptions({
  name: COMP_NAME
})

defineProps<CardProps>()

// CSS Houdini Paint Worklet
const paint = () => {
  try {
    if ('paintWorklet' in CSS) {
      ;(CSS as any).paintWorklet.addModule(workletURL)
    } else {
      debugWarn(
        COMP_NAME,
        'CSS Houdini Paint Worklet API is not supported in this browser.'
      )
    }
    // (CSS as any).paintWorklet.addModule(workletURL)
  } catch (error) {
    console.error('Error loading Paint Worklet:', error)
  }
}

onMounted(async () => {
  paint()
})
</script>

<template>
  <div
    class="px-card"
    :class="{
      [`px-card--hover`]: hoverable,
      [`is-round`]: round,
      [`is-circle`]: circle
    }"
  >
    <div v-if="$slots.prepend" class="px-card__icon">
      <slot name="prepend"></slot>
    </div>
    <div class="px-card__content">
      <div v-if="$slots.header" class="px-card__header">
        <slot name="header"></slot>
      </div>
      <div v-if="$slots.default" class="px-card__body">
        <slot></slot>
      </div>
      <div v-if="$slots.footer" class="px-card__footer">
        <slot name="footer"></slot>
      </div>
    </div>
    <div v-if="$slots.append" class="px-card__append">
      <slot name="append"></slot>
    </div>
  </div>
</template>

<style scoped>
@import './style.css';
</style>
