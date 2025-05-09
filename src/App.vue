<script setup lang="ts">
import { ref } from 'vue'
import ClipboardInput from './components/ClipboardInput.vue'
import ClipboardGrid from './components/ClipboardGrid.vue'
import ClipboardTile from './components/ClipboardTile.vue'

interface ClipboardItem {
  id: string
  content: string
}

const clipboardItems = ref<ClipboardItem[]>([])
const showThanksToast = ref(false)

function addClipboardItem(content: string) {
  clipboardItems.value.unshift({
    id: Date.now().toString() + Math.random().toString(36).slice(2),
    content
  })
}

function copyToClipboard(content: string) {
  navigator.clipboard.writeText(content)
}

function deleteClipboardItem(id: string) {
  clipboardItems.value = clipboardItems.value.filter(item => item.id !== id)
}

function clearClipboard() {
  clipboardItems.value = []
}

function showThanks() {
  showThanksToast.value = true
  setTimeout(() => showThanksToast.value = false, 1500)
}
</script>

<template>
  <div id="app">
    <div v-if="showThanksToast" class="copied-toast thanks-toast">Thanks for using ClipBuddy!</div>
    <h1 class="app-title" @dblclick="showThanks">ClipBuddy</h1>
    <div class="input-row">
      <ClipboardInput class="flex-1" @add="addClipboardItem" />
    </div>
    <div class="grid-container">
      <ClipboardGrid>
        <ClipboardTile
          v-for="item in clipboardItems"
          :key="item.id"
          :content="item.content"
          @copy="copyToClipboard(item.content)"
          @delete="deleteClipboardItem(item.id)"
        />
      </ClipboardGrid>
    </div>
    <div class="clear-btn-row">
      <button
          class="add-btn"
          :disabled="clipboardItems.length === 0"
          @click="clearClipboard"
        >
          Clear All
        </button>
    </div>
  </div>
</template>

<style>
</style>
