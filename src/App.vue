<script setup lang="ts">
import { ref, onMounted, watch } from 'vue'
import ClipboardInput from './components/ClipboardInput.vue'
import ClipboardTile from './components/ClipboardTile.vue'
import draggable from 'vuedraggable'

interface ClipboardItem {
  id: string
  content: string
  type: 'text' | 'image'
}

const clipboardItems = ref<ClipboardItem[]>([])
const showThanksToast = ref(false)
const showHelp = ref(false)
const isDarkTheme = ref(false)

// Declare global variables
declare const __APP_VERSION__: string
declare const __BUILD_DATE__: string

const version = __APP_VERSION__
const buildDate = (() => {
  const date = new Date(__BUILD_DATE__)
  const pad = (n: number) => n.toString().padStart(2, '0')
  return `${pad(date.getDate())}-${pad(date.getMonth() + 1)}-${date.getFullYear()} - ${pad(date.getHours())}:${pad(date.getMinutes())}:${pad(date.getSeconds())}`
})()

// Load theme preference from localStorage
onMounted(() => {
  const savedTheme = localStorage.getItem('theme')
  if (savedTheme === 'dark') {
    isDarkTheme.value = true
    document.documentElement.setAttribute('data-theme', 'dark')
  }

  // Add demo items
  const demoItems: { content: string; type: 'text' | 'image' }[] = [
    { content: "📋 Welcome to ClipBuddy! Double-click any item to copy it to your clipboard.", type: 'text' },
    { content: "git clone https://github.com/yourusername/clip-buddy.git", type: 'text' },
    { content: "Why do programmers prefer dark mode? Because light attracts bugs! 🐛", type: 'text' },
    { content: "npm install && npm run dev", type: 'text' },
    { content: "const greeting = 'Hello, World!';\nconsole.log(greeting);", type: 'text' },
    { content: "https://vuejs.org/guide/introduction.html", type: 'text' },
    { content: "📧 your.email@example.com", type: 'text' },
    { content: "Why did the JavaScript developer go broke? Because he used up all his cache! 💸", type: 'text' },
    { content: "📱 +1 (555) 123-4567", type: 'text' },
    { content: "🔑 API_KEY=your_secret_key_here", type: 'text' },
    { content: "📝 Meeting Notes:\n- Discuss project timeline\n- Review design mockups\n- Plan next sprint", type: 'text' },
    { content: "Why don't scientists trust atoms? Because they make up everything! ⚛️", type: 'text' },
    { content: "📋 Quick Tips:\n1. Use the theme toggle in the top left\n2. Hover over items for copy/delete options\n3. Press Enter to add new items\n4. Paste images directly to add them!", type: 'text' },
    { content: "What do you call a computer that sings? A Dell! 🎵", type: 'text' },
  ]

  demoItems.forEach(item => addClipboardItem(item.content, item.type))
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

function addClipboardItem(content: string, type: 'text' | 'image' = 'text') {
  clipboardItems.value.unshift({
    id: Date.now().toString() + Math.random().toString(36).slice(2),
    content,
    type
  })
}

function copyToClipboard(content: string, type: 'text' | 'image') {
  if (type === 'text') {
    navigator.clipboard.writeText(content)
  } else {
    // For images, we need to convert the base64 to a blob and write it to clipboard
    fetch(content)
      .then(res => res.blob())
      .then(blob => {
        const item = new ClipboardItem({ 'image/png': blob })
        navigator.clipboard.write([item])
      })
  }
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
        <h3>How to use ClipBuddy</h3>
        <ul>
          <li>Add items by typing and pressing Enter, or paste text/images directly into the input field</li>
          <li>Copy items by double-clicking or using the copy button (hover to see buttons)</li>
          <li>Drag items to reorder them, or use the delete button to remove them</li>
          <li>Use "Clear All" to remove all items, and the toggle on the top left to change theme</li>
        </ul>
        <div class="version-info">
          <span>v{{ version }}</span>
          <span>[{{ buildDate }}]</span>
        </div>
      </div>
    </div>
    <h1 class="app-title" @dblclick="showThanks">ClipBuddy</h1>
    <div class="input-row">
      <ClipboardInput class="flex-1" @add="addClipboardItem" />
    </div>
    <div class="grid-container">
      <draggable
        v-model="clipboardItems"
        item-key="id"
        class="clipboard-grid"
        :animation="200"
        ghost-class="ghost"
        drag-class="drag"
      >
        <template #item="{ element }">
          <ClipboardTile
            :content="element.content"
            :type="element.type"
            @copy="copyToClipboard(element.content, element.type)"
            @delete="deleteClipboardItem(element.id)"
          />
        </template>
      </draggable>
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
.version-info {
  margin-top: 1rem;
  padding-top: 0.5rem;
  border-top: 1px solid var(--nb-border);
  font-size: 0.8rem;
  color: var(--nb-text);
  opacity: 0.8;
  display: flex;
  justify-content: space-between;
}

.ghost {
  opacity: 0.5;
  background: var(--nb-pink);
}

.drag {
  cursor: grabbing;
}

.clipboard-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
  gap: 1.5rem;
  width: 100%;
  min-height: 300px;
  box-sizing: border-box;
}
</style>
