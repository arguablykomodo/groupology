<script setup>
import { ref } from 'vue'

import { sample_queries } from '../data/sample_queries.js'

const props = defineProps({
    groups: Object
})

const currentGroupKey = ref(null)
const editGroupName = ref('')
const newCategoryName = ref('')
const isFetching = ref(false)
const sparqlQuery = ref(null)

const defaultColors = ['#ffb3ba', '#ffdfba', '#ffffba', '#baffc9', '#bae1ff', '#e8baff', '#d4a5a5', '#9b89b3']

function loadExampleQuery(queryText) {
    sparqlQuery.value = queryText
}

function selectGroup(key) {
    currentGroupKey.value = key
    editGroupName.value = key
}

function renameGroup() {
    const newName = editGroupName.value.trim()
    const oldName = currentGroupKey.value

    // if empty or hasn't changed, revert back and do nothing
    if (!newName || newName === oldName) {
        editGroupName.value = oldName
        return
    }

    // prevent overwriting an existing group
    if (props.groups[newName]) {
        alert("A category with this name already exists!")
        editGroupName.value = oldName
        return
    }

    props.groups[newName] = props.groups[oldName]
    delete props.groups[oldName]
    currentGroupKey.value = newName
}

function addItem() {
    props.groups[currentGroupKey.value].items.push('New Item')
}

function removeItem(index) {
    props.groups[currentGroupKey.value].items.splice(index, 1)
}

function addCategory() {
    const name = newCategoryName.value.trim()
    if (name && !props.groups[name]) {
        const colorIndex = Object.keys(props.groups).length % defaultColors.length

        props.groups[name] = {
            items: [],
            color: defaultColors[colorIndex]
        }

        selectGroup(name)
        newCategoryName.value = ''
    }
}

async function populateFromWikidata() {
    if (!currentGroupKey.value) return
    isFetching.value = true

    try {
        const url = `https://query.wikidata.org/sparql?query=${encodeURIComponent(sparqlQuery.value)}`
        const response = await fetch(url, { headers: { 'Accept': 'application/sparql-results+json' } })
        const data = await response.json()

        const newItems = data.results.bindings.map(binding => binding.itemLabel.value)
        props.groups[currentGroupKey.value].items.push(...newItems)
        props.groups[currentGroupKey.value].items = [...new Set(props.groups[currentGroupKey.value].items)]
    } catch (error) {
        alert("Failed to fetch from Wikidata : " + error.message)
    } finally {
        isFetching.value = false
    }
}
</script>

<template>
    <div class="group-editor">
        
        <div class="add-category-panel">
            <strong>Add New Category : </strong>
            <input v-model="newCategoryName" @keyup.enter="addCategory">
            <button class="btn" @click="addCategory">Add</button>
        </div>

        <div class="categories-list">
            <span v-for="(groupData, key) in groups" :key="key" class="category-item">
                <button class="btn" :class="{ 'active-category': currentGroupKey === key }"
                    :style="{ backgroundColor: groupData.color }" @click="selectGroup(key)">
                    {{ key }} ({{ groupData.items.length }})
                </button>
            </span>
        </div>

        <div v-if="currentGroupKey" class="editor-layout">
            
            <div class="manual-column">
                <div class="edit-header">
                    <h3>Editing:</h3>

                    <input v-model="editGroupName" class="group-name-input" title="Rename group" @blur="renameGroup"
                        @keyup.enter="$event.target.blur()">

                    <input type="color" v-model="groups[currentGroupKey].color" class="color-picker"
                        title="Change group color">
                </div>

                <div v-for="(element, index) in groups[currentGroupKey].items" :key="index" class="item-row">
                    <input v-model="groups[currentGroupKey].items[index]">
                    <button class="btn remove-item-btn" @click="removeItem(index)">X</button>
                </div>
                <button class="btn add-item-btn" @click="addItem">+ Add Item</button>
            </div>

            <div class="wikidata-column">
                <h3>Populate via Wikidata</h3>
                <textarea class="sparql-input" v-model="sparqlQuery" rows="6"></textarea>
                <button class="btn" @click="populateFromWikidata" :disabled="isFetching">
                    {{ isFetching ? 'Fetching...' : 'Run Query' }}
                </button>

                <div class="examples-section">
                    <h4>Example Queries:</h4>
                    <ul class="examples-list">
                        <li v-for="(queryText, queryName) in sample_queries" :key="queryName">
                            <span>{{ queryName }}</span>
                            <button class="btn load-btn" @click="loadExampleQuery(queryText)">Load</button>
                        </li>
                    </ul>
                </div>
            </div>
            
        </div>
    </div>
</template>

<style scoped>
.add-category-panel {
    margin-bottom: 20px; 
    padding: 10px; 
    background: #eee;
    border-radius: 4px;
}

.categories-list {
    margin-bottom: 20px;
}

.category-item {
    margin-right: 5px;
}

.active-category {
    font-weight: bold;
}

.editor-layout {
    display: flex; 
    gap: 20px;
}

.manual-column {
    flex: 1;
}

.edit-header {
    display: flex;
    align-items: center;
    gap: 15px;
    margin-bottom: 10px;
}

.edit-header h3 {
    margin: 0;
}

.group-name-input {
    font-size: 1.17em;
    font-weight: bold;
    padding: 2px 6px;
    border: 1px solid #ccc;
    border-radius: 4px;
    width: 200px;
}

.color-picker {
    cursor: pointer;
    padding: 0;
    border: none;
    height: 30px;
    width: 30px;
    border-radius: 4px;
}

.item-row {
    margin-bottom: 5px;
}

.remove-item-btn {
    margin-left: 5px;
}

.add-item-btn {
    margin-top: 10px;
}

.wikidata-column {
    flex: 1; 
    padding: 15px; 
    border: 1px solid #ccc; 
    background: #f9f9f9;
    border-radius: 4px;
}

.sparql-input {
    width: 100%;
    margin-bottom: 10px;
    box-sizing: border-box; /* prevents text area from breaking out of the container */
    resize: vertical; /* allows user to make it taller, but not wider */
}

.examples-section {
    margin-top: 25px;
    border-top: 1px solid #ddd;
    padding-top: 10px;
}

.examples-list {
    list-style-type: none;
    padding: 0;
    margin: 0;
}

.examples-list li {
    display: flex;
    justify-content: space-between;
    align-items: center;
    background: #fff;
    padding: 6px 10px;
    border: 1px solid #eee;
    margin-bottom: 5px;
    border-radius: 3px;
    font-size: 0.9em;
}

.load-btn {
    padding: 2px 8px;
    font-size: 0.8em;
    cursor: pointer;
}
</style>