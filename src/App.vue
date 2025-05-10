<script setup lang="ts">
import { ref, onMounted, watch } from 'vue'
import ClipboardInput from './components/ClipboardInput.vue'
import ClipboardGrid from './components/ClipboardGrid.vue'
import ClipboardTile from './components/ClipboardTile.vue'

interface ClipboardItem {
  id: string
  content: string
}

const clipboardItems = ref<ClipboardItem[]>([])
const showThanksToast = ref(false)
const showHelp = ref(false)
const isDarkTheme = ref(false)

// Load theme preference from localStorage
onMounted(() => {
  const savedTheme = localStorage.getItem('theme')
  if (savedTheme === 'dark') {
    isDarkTheme.value = true
    document.documentElement.setAttribute('data-theme', 'dark')
  }
})

// Watch for theme changes
watch(isDarkTheme, (newValue) => {
  if (newValue) {
    document.documentElement.setAttribute('data-theme', 'dark')
    localStorage.setItem('theme', 'dark')
  } else {
    document.documentElement.removeAttribute('data-theme')
    localStorage.setItem('theme', 'light')
  }
})

for (let i = 0; i < 10; i++) {
  addClipboardItem("Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.")
}

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
    <div class="theme-toggle">
      <label class="switch">
        <input type="checkbox" v-model="isDarkTheme">
        <span class="theme-slider"></span>
      </label>
    </div>
    <div class="help-container">
      <button class="help-btn" @click="showHelp = !showHelp" title="Help">
        <svg class="help-icon" xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960" width="24px" fill="currentColor"><path d="M440-280h80v-240h-80v240Zm40-320q17 0 28.5-11.5T520-640q0-17-11.5-28.5T480-680q-17 0-28.5 11.5T440-640q0 17 11.5 28.5T480-600Zm0 520q-83 0-156-31.5T197-197q-54-54-85.5-127T80-480q0-83 31.5-156T197-763q54-54 127-85.5T480-880q83 0 156 31.5T763-763q54 54 85.5 127T880-480q0 83-31.5 156T763-197q-54 54-127 85.5T480-80Zm0-80q134 0 227-93t93-227q0-134-93-227t-227-93q-134 0-227 93t-93 227q0 134 93 227t227 93Zm0-320Z"/></svg>
      </button>
      <div v-if="showHelp" class="help-tooltip">
        <h3>How to use ClipBuddy:</h3>
        <ul>
          <li>Type text in the input field and click "Add" or press Enter to add a new item</li>
          <li>Double-click any item to copy its content</li>
          <li>Hover over an item to see copy and delete buttons</li>
          <li>Click the copy button to copy the content</li>
          <li>Click the delete button to remove an item</li>
          <li>Use "Clear All" to remove all items</li>
          <li>Use the toggle on the top left to change the theme</li>
        </ul>
      </div>
    </div>
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
