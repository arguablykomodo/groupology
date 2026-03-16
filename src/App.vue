<script setup>
import { ref, computed } from 'vue'
import GroupEditor from './components/GroupEditor.vue'
import Game from './components/Game.vue'

// shared data
const sharedGroups = ref({})

// start on the setup screen
const currentView = ref('editor')

const requireEqualSizes = ref(false)

const canPlay = computed(() => {
  const groups = Object.values(sharedGroups.value)
  
  const categoriesWithTwoOrMore = groups.filter(group => group.items.length >= 2)
  if (categoriesWithTwoOrMore.length < 2) {
    return false
  }

  if (requireEqualSizes.value) {
    if (groups.length === 0) return false
    const firstGroupSize = groups[0].items.length
    const allSameSize = groups.every(group => group.items.length === firstGroupSize)
    
    if (!allSameSize) return false
  }

  return true
})

const playTooltip = computed(() => {
  if (canPlay.value) return ''

  const groups = Object.values(sharedGroups.value)
  const categoriesWithTwoOrMore = groups.filter(group => group.length >= 2)

  if (categoriesWithTwoOrMore.length < 2) {
    return 'Needs at least 2 categories with 2 or more items'
  }

  if (requireEqualSizes.value) {
    return 'All categories must have the exact same number of items'
  }

  return ''
})

</script>

<template>
  <div>

    <div v-if="currentView === 'editor'">
      <div class="play-container">
        <div class="options-container">
          <label>
            <input class="equal-size-check" type="checkbox" v-model="requireEqualSizes">
            All categories must be the same size
          </label>
        </div>
        <button class="play-button" :disabled="!canPlay" :title="playTooltip" @click="currentView = 'project'">
          Play !
        </button>
      </div>

      <GroupEditor :groups="sharedGroups" />
    </div>

    <div v-if="currentView === 'project'">
      <Game :groups="sharedGroups" />
    </div>

  </div>
</template>

<!-- global style -->
<style>
* {
  font-family: sans-serif;
}
</style>

<style scoped>
.play-container {
  text-align: center;
  margin-bottom: 10px 0;
  background: #ddd;
  border-radius: 4px;
}

.equal-size-check {
  margin-top: 20px;
  margin-bottom: 20px;
}

.play-button {
  padding: 15px;
  font-size: 20px;
  font-weight: bold;
  cursor: pointer;
  margin-bottom: 10px;
}
</style>