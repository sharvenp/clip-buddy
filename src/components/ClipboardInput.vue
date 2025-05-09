<template>
  <div class="clipboard-input">
    <input
      v-model="input"
      class="input-field"
      placeholder="Enter something..."
      type="text"
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
function submit() {
  if (input.value.trim()) {
    emit("add", input.value.trim())
    input.value = ""
  }
}
</script>