<script setup>
import { computed } from 'vue'
import { ToastViewport } from 'radix-vue'
import { cn } from '@/lib/utils'

const props = defineProps({
  hotkey: { type: Array, required: false },
  label: { type: String, required: false },
  asChild: { type: Boolean, required: false },
  as: { type: null, required: false },
  class: { type: null, required: false }
})

const delegatedProps = computed(() => {
  const { class: _, ...delegated } = props

  return delegated
})
</script>

<template>
  <ToastViewport
    v-bind="delegatedProps"
    :class="
      cn(
        // 'fixed top-0 z-[100] flex max-h-screen w-full flex-col-reverse p-4 sm:bottom-0 sm:right-0 sm:top-auto sm:flex-col md:max-w-[420px]',
        'top-0 right-0 z-[100] flex fixed w-80 md:max-w-[420px] md:top-4 md:right-4',
        props.class
      )
    "
  />
</template>
