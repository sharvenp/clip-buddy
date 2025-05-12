<template>
  <div class="clipboard-input">
    <input
      v-model="input"
      class="input-field"
      placeholder="Enter something or paste an image..."
      type="text"
      @paste="handlePaste"
    />
    <button
      class="add-btn"
      :disabled="!input.trim()"
      @click="submit"
    >
      Add
    </button>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
const emit = defineEmits(['add'])

const input = ref("")

async function handlePaste(e: ClipboardEvent) {
  e.preventDefault()

  // Check for image data
  const items = e.clipboardData?.items
  if (items) {
    for (const item of items) {
      if (item.type.indexOf('image') !== -1) {
        const file = item.getAsFile()
        if (file) {
          const reader = new FileReader()
          reader.onload = (e) => {
            const result = e.target?.result
            if (typeof result === 'string') {
              emit("add", result, 'image')
            }
          }
          reader.readAsDataURL(file)
          return
        }
      }
    }
  }

  // Handle text paste
  const pastedText = e.clipboardData?.getData('text')
  if (pastedText?.trim()) {
    emit("add", pastedText.trim(), 'text')
    input.value = ""
  }
}

function submit() {
  if (input.value.trim()) {
    emit("add", input.value.trim(), 'text')
    input.value = ""
  }
}
</script>