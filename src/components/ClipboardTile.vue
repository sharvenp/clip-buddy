<template>
  <div class="clipboard-tile" @dblclick="handleTileDblClick">
    <div v-if="copied" class="copied-toast">Copied!</div>
    <button
      class="delete-btn"
      @click.stop="emit('delete')"
      title="Delete"
    >
      🗑️
    </button>
    <button
      class="copy-btn"
      @click.stop="triggerCopy"
      title="Copy to clipboard"
    >
      📋
    </button>
    <div class="tile-content">
      {{ content }}
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
const props = defineProps<{ content: string }>()
const emit = defineEmits(['copy', 'delete'])

const copied = ref(false)
function handleTileDblClick(e: MouseEvent) {
  if (!(e.target as HTMLElement).closest('button')) {
    triggerCopy()
  }
}
function triggerCopy() {
  emit('copy', props.content)
  copied.value = true
  setTimeout(() => copied.value = false, 1000)
}
</script>

<style scoped>
.scrollbar-hide::-webkit-scrollbar {
  display: none;
}
.scrollbar-hide {
  -ms-overflow-style: none;
  scrollbar-width: none;
}
</style>