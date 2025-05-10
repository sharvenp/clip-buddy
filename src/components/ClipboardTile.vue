<template>
  <div class="clipboard-tile" @dblclick="handleTileDblClick">
    <div v-if="copied" class="copied-toast">Copied!</div>
    <button
      class="delete-btn"
      @click.stop="emit('delete')"
      title="Delete"
    >
    <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960" width="24px" fill="currentColor"><path d="M280-120q-33 0-56.5-23.5T200-200v-520h-40v-80h200v-40h240v40h200v80h-40v520q0 33-23.5 56.5T680-120H280Zm400-600H280v520h400v-520ZM360-280h80v-360h-80v360Zm160 0h80v-360h-80v360ZM280-720v520-520Z"/></svg>
    </button>
    <button
      class="copy-btn"
      @click.stop="triggerCopy"
      title="Copy to clipboard"
    >
    <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960" width="24px" fill="currentColor"><path d="M200-120q-33 0-56.5-23.5T120-200v-560q0-33 23.5-56.5T200-840h167q11-35 43-57.5t70-22.5q40 0 71.5 22.5T594-840h166q33 0 56.5 23.5T840-760v560q0 33-23.5 56.5T760-120H200Zm0-80h560v-560h-80v120H280v-120h-80v560Zm280-560q17 0 28.5-11.5T520-800q0-17-11.5-28.5T480-840q-17 0-28.5 11.5T440-800q0 17 11.5 28.5T480-760Z"/></svg>
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
  setTimeout(() => copied.value = false, 1500)
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