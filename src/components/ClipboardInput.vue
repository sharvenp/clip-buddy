<template>
  <div class="clipboard-input">
    <input
      v-model="input"
      class="input-field"
      placeholder="Enter something..."
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

function handlePaste(e: ClipboardEvent) {
  e.preventDefault()
  const pastedText = e.clipboardData?.getData('text')
  if (pastedText?.trim()) {
    emit("add", pastedText.trim())
    input.value = ""
  }
}

function submit() {
  if (input.value.trim()) {
    emit("add", input.value.trim())
    input.value = ""
  }
}
</script>