<script setup>
import { ref } from 'vue'

const props = defineProps({
  items: { type: Array, required: true },
  score: { type: Number, required: true },
  mistakes: { type: Number, required: true },
  groupAmount: { type: Number, required: true },
  groupSize: { type: Number, required: true },
})

const emit = defineEmits(['update:items', 'update:score', 'update:mistakes'])

const selectedItem = ref(null)

function deselect() {
  selectedItem.value = null
}

function selectItem(item) {
  if (selectedItem.value == null) {
    selectedItem.value = item
    return
  }

  if (item === selectedItem.value) {
    deselect()
    return
  }

  if (item.category === selectedItem.value.category) {
    emit('update:score', props.score + 1)

    const other = selectedItem.value
    const updatedItem = { ...item, names: [...item.names, ...other.names] }

    const nextItems = props.items
      .filter(i => i !== other)
      .map(i => (i === item ? updatedItem : i))

    emit('update:items', nextItems)
  } else {
    emit('update:mistakes', props.mistakes + 1)
  }

  deselect()
}
</script>

<template>
  <p>
    Make {{ groupAmount }} groups of {{ groupSize }}! (By combining two at a time.)
    Score: {{ score }} Mistakes: {{ mistakes }}
  </p>

  <div v-if="items.length > 0">
    <span v-for="item in items" :key="item.id">
      <button
        class="item-button"
        :class="{ selected: item === selectedItem, bold: item.names.length > 1 }"
        :title="item.names.join('\n')"
        :disabled="item.names.length >= groupSize"
        @click="selectItem(item)"
      >
        <template v-if="item.names.length >= groupSize">
          {{ item.category }}
        </template>
        <template v-else>
          {{ item.names.slice(0, 3).join('\n') }}
          <span v-if="item.names.length > 3"><br />...</span>
          <span v-if="item.names.length > 2" class="count-text"><br /> [{{ item.names.length }}]</span>
        </template>
      </button>
    </span>
  </div>
</template>

<style scoped>
.item-button {
  white-space: pre-line;
  margin: 5px;
}

.item-button.selected {
  background-color: black;
  color: white;
  border-color: white;
}

.bold {
  font-weight: bold;
}

.count-text {
  color: red;
}
</style>
