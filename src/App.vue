<script setup>
import { ref, onMounted } from 'vue'

const API_URL = `https://query.wikidata.org/bigdata/namespace/wdq/sparql?format=json&query=`

/*
const queries = {
  'Elements': 'SELECT ?itemLabel {{SELECT DISTINCT ?itemLabel WHERE {?item wdt:P31 wd:Q11344 ; SERVICE wikibase:label {bd:serviceParam wikibase:language "[AUTO_LANGUAGE],mul,en" }} LIMIT 45}FILTER lang(?itemLabel)}',
  'African Countries': 'SELECT ?itemLabel {{SELECT DISTINCT ?itemLabel WHERE {?item wdt:P31 wd:Q6256 ; wdt:P30 wd:Q15 ; SERVICE wikibase:label {bd:serviceParam wikibase:language "[AUTO_LANGUAGE],mul,en" }} LIMIT 45}FILTER lang(?itemLabel)}',
  'Muppets': 'SELECT ?itemLabel {{SELECT DISTINCT ?itemLabel WHERE {VALUES ?types {wd:Q2120540 wd:Q155629} ?item wdt:P1441 ?types; SERVICE wikibase:label {bd:serviceParam wikibase:language "[AUTO_LANGUAGE],mul,en" }} LIMIT 45}FILTER lang(?itemLabel)}',
  'US State Capitals': 'SELECT ?capitalLabel {{SELECT DISTINCT ?capitalLabel WHERE {?item wdt:P31 wd:Q35657 ; wdt:P36 ?capital ; SERVICE wikibase:label {bd:serviceParam wikibase:language "[AUTO_LANGUAGE],mul,en" }} LIMIT 45}FILTER lang(?capitalLabel)}',
  // 'Prepositions': '', // not available on wikidata
  // 'Types of Numbers': '', // only 40 : SELECT ?itemLabel {{SELECT DISTINCT ?itemLabel WHERE {?item wdt:P31 wd:Q47460393 ; SERVICE wikibase:label {bd:serviceParam wikibase:language "[AUTO_LANGUAGE],mul,en" }} LIMIT 45}FILTER lang(?itemLabel)}
  'Internet Memes': 'SELECT ?itemLabel {{SELECT DISTINCT ?itemLabel WHERE {?item wdt:P31 wd:Q2927074 ; SERVICE wikibase:label {bd:serviceParam wikibase:language "[AUTO_LANGUAGE],mul,en" }} LIMIT 45}FILTER lang(?itemLabel)}',
  'Mammals': 'SELECT ?singleCommonName WHERE {SELECT ?item (SAMPLE(?commonName) AS ?singleCommonName) ?sitelinks WHERE {?item wdt:P171+ wd:Q7377 FILTER(NOT EXISTS { ?item wdt:P31 wd:Q23038290. }) ?item wdt:P1843 ?commonName FILTER((LANG(?commonName)) = "en") ?item wikibase:sitelinks ?sitelinks} GROUP BY ?item ?sitelinks} ORDER BY DESC (?sitelinks) LIMIT 45',
  'Musical Instruments': 'SELECT ?itemLabel {{SELECT DISTINCT ?itemLabel WHERE {?item wdt:P31 wd:Q110295396 ; SERVICE wikibase:label {bd:serviceParam wikibase:language "[AUTO_LANGUAGE],mul,en" }} LIMIT 45}FILTER lang(?itemLabel)}',
  'Dog Breeds': 'SELECT ?itemLabel {{SELECT DISTINCT ?itemLabel WHERE {?item wdt:P31 wd:Q39367 ; SERVICE wikibase:label {bd:serviceParam wikibase:language "[AUTO_LANGUAGE],mul,en" }} LIMIT 45}FILTER lang(?itemLabel)}',
  'Olympic Sports': 'SELECT ?itemLabel {{SELECT DISTINCT ?itemLabel WHERE {?item wdt:P31 wd:Q212434 ; SERVICE wikibase:label {bd:serviceParam wikibase:language "[AUTO_LANGUAGE],mul,en" }} LIMIT 45}FILTER lang(?itemLabel)}',
  'D&D Monsters': 'SELECT ?itemLabel {{SELECT DISTINCT ?itemLabel WHERE {?item wdt:P279 wd:Q107765406; wdt:P1441 wd:Q1375; SERVICE wikibase:label {bd:serviceParam wikibase:language "[AUTO_LANGUAGE],mul,en" }} LIMIT 45}FILTER lang(?itemLabel)}',
  'Tolkien Characters': 'SELECT ?itemLabel {{SELECT DISTINCT ?itemLabel WHERE {?item wdt:P170 wd:Q892 ; wdt:P21 ?any ; SERVICE wikibase:label {bd:serviceParam wikibase:language "[AUTO_LANGUAGE],mul,en" }} LIMIT 45}FILTER lang(?itemLabel)}',
  'Cartoonists': 'SELECT ?itemLabel {{SELECT DISTINCT ?itemLabel WHERE {?item wdt:P106 wd:Q715301 ; SERVICE wikibase:label {bd:serviceParam wikibase:language "[AUTO_LANGUAGE],mul,en" }} LIMIT 45}FILTER lang(?itemLabel)}',
  'US VPs': 'SELECT ?itemLabel {{SELECT DISTINCT ?itemLabel WHERE {?item wdt:P39 wd:Q11699 ; SERVICE wikibase:label {bd:serviceParam wikibase:language "[AUTO_LANGUAGE],mul,en" }} LIMIT 45}FILTER lang(?itemLabel)}',
  // 'SNL Cast Members': '',  // only 26 : SELECT ?itemLabel {{SELECT DISTINCT ?itemLabel WHERE {wd:Q13979 wdt:P161 ?item ; SERVICE wikibase:label {bd:serviceParam wikibase:language "[AUTO_LANGUAGE],mul,en" }} LIMIT 45}FILTER lang(?itemLabel)}
  'Tourist Destinations': 'SELECT ?itemLabel {{SELECT DISTINCT ?itemLabel WHERE {?item wdt:P31 wd:Q570116; SERVICE wikibase:label {bd:serviceParam wikibase:language "[AUTO_LANGUAGE],mul,en" }} LIMIT 45}FILTER lang(?itemLabel)}',
  'College Majors': 'SELECT ?itemLabel {{SELECT DISTINCT ?itemLabel WHERE {?item wdt:P31 wd:Q4671286 ; SERVICE wikibase:label {bd:serviceParam wikibase:language "[AUTO_LANGUAGE],mul,en" }} LIMIT 45}FILTER lang(?itemLabel)}',
  'Car Brands': 'SELECT ?itemLabel {{SELECT DISTINCT ?itemLabel WHERE {?item wdt:P31 wd:Q10429667; SERVICE wikibase:label {bd:serviceParam wikibase:language "[AUTO_LANGUAGE],mul,en" }} LIMIT 45}FILTER lang(?itemLabel)}',
  'Largest Publicly Traded Companies': 'SELECT ?itemLabel {{SELECT DISTINCT ?itemLabel WHERE {?item wdt:P361 wd:Q773026; SERVICE wikibase:label {bd:serviceParam wikibase:language "[AUTO_LANGUAGE],mul,en" }} LIMIT 45}FILTER lang(?itemLabel)}',
  // 'Collective Nouns': '',  // afaik not on wikidata
  'Programming Languages': 'SELECT ?itemLabel {{SELECT DISTINCT ?itemLabel WHERE {?item wdt:P31 wd:Q9143 ; SERVICE wikibase:label {bd:serviceParam wikibase:language "[AUTO_LANGUAGE],mul,en" }} LIMIT 45}FILTER lang(?itemLabel)}',
  'Cheeses': 'SELECT ?itemLabel {{SELECT DISTINCT ?itemLabel WHERE {VALUES ?types {wd:Q3088299 wd:Q198815 wd:Q1411808} ?item wdt:P279 ?types; SERVICE wikibase:label {bd:serviceParam wikibase:language "[AUTO_LANGUAGE],mul,en" }} LIMIT 45}FILTER lang(?itemLabel)}',
  'Pastas': 'SELECT ?itemLabel {{SELECT DISTINCT ?itemLabel WHERE {?item wdt:P31 wd:Q2625877; SERVICE wikibase:label {bd:serviceParam wikibase:language "[AUTO_LANGUAGE],mul,en" }} LIMIT 45}FILTER lang(?itemLabel)}',
  'MCU Characters': 'SELECT ?itemLabel {{SELECT DISTINCT ?itemLabel WHERE {?item wdt:P1080 wd:Q642878; SERVICE wikibase:label {bd:serviceParam wikibase:language "[AUTO_LANGUAGE],mul,en" }} LIMIT 45}FILTER lang(?itemLabel)}',
  'Rock Bands': 'SELECT ?itemLabel {{SELECT DISTINCT ?itemLabel WHERE {?item wdt:P31 wd:Q215380 ; wdt:P136 wd:Q11399 ; SERVICE wikibase:label {bd:serviceParam wikibase:language "[AUTO_LANGUAGE],mul,en" }} LIMIT 45}FILTER lang(?itemLabel)}',
  'Birds': 'SELECT ?itemLabel {{SELECT DISTINCT ?itemLabel WHERE {?item wdt:P10007 ?any ; SERVICE wikibase:label {bd:serviceParam wikibase:language "[AUTO_LANGUAGE],mul,en" }} LIMIT 45}FILTER lang(?itemLabel)}',
  'Tom Hanks Movies': 'SELECT ?itemLabel {{SELECT DISTINCT ?itemLabel WHERE {?item wdt:P161 wd:Q2263 ; SERVICE wikibase:label {bd:serviceParam wikibase:language "[AUTO_LANGUAGE],mul,en" }} LIMIT 45}FILTER lang(?itemLabel)}',
  'Celebrated 20th Century Novels': 'SELECT ?itemLabel {{SELECT DISTINCT ?itemLabel WHERE {?item wdt:P166 wd:Q4630676 ; SERVICE wikibase:label {bd:serviceParam wikibase:language "[AUTO_LANGUAGE],mul,en" }} LIMIT 45}FILTER lang(?itemLabel)}',
  'Trees': 'SELECT (SAMPLE(?commonName) AS ?singleCommonName) WHERE {?item (wdt:P279+) wd:Q10884 ; wdt:P1843 ?commonName ; FILTER((LANG(?commonName)) = "en")} GROUP BY ?item LIMIT 45',
  'Fallacies': 'SELECT ?itemLabel WHERE {?item wdt:P279+ wd:Q186150 ; FILTER NOT EXISTS {?child wdt:P279 ?item} SERVICE wikibase:label { bd:serviceParam wikibase:language "en". }} LIMIT 45',
  'Google Products': 'SELECT ?itemLabel {{SELECT DISTINCT ?itemLabel WHERE {?item wdt:P178 wd:Q95; SERVICE wikibase:label {bd:serviceParam wikibase:language "[AUTO_LANGUAGE],mul,en" }} LIMIT 45} FILTER lang(?itemLabel)}',
  'Minerals': 'SELECT ?itemLabel {{SELECT DISTINCT ?itemLabel WHERE {?item wdt:P31 wd:Q429795; SERVICE wikibase:label {bd:serviceParam wikibase:language "[AUTO_LANGUAGE],mul,en" }} LIMIT 45}FILTER lang(?itemLabel)}',
  'Books of the Bible': 'SELECT ?itemLabel {{SELECT DISTINCT ?itemLabel WHERE {?item wdt:P31 wd:Q29154430; SERVICE wikibase:label {bd:serviceParam wikibase:language "[AUTO_LANGUAGE],mul,en" }} LIMIT 45} FILTER lang(?itemLabel)}',
  'Beatles Songs': 'SELECT ?itemLabel {{SELECT DISTINCT ?itemLabel WHERE {?item wdt:P175 wd:Q1299; SERVICE wikibase:label {bd:serviceParam wikibase:language "[AUTO_LANGUAGE],mul,en" }} LIMIT 45} FILTER lang(?itemLabel)}',
  'Cocktails': 'SELECT ?itemLabel {{SELECT DISTINCT ?itemLabel WHERE {?item wdt:P31 wd:Q2536409; SERVICE wikibase:label {bd:serviceParam wikibase:language "[AUTO_LANGUAGE],mul,en" }} LIMIT 45} FILTER lang(?itemLabel)}',
  'Flowers': 'SELECT ?itemLabel {{SELECT DISTINCT ?itemLabel WHERE {?item wdt:P31 wd:Q506; SERVICE wikibase:label {bd:serviceParam wikibase:language "[AUTO_LANGUAGE],mul,en" }} LIMIT 45}FILTER lang(?itemLabel)}',
  'Fabrics': 'SELECT ?itemLabel {{SELECT DISTINCT ?itemLabel WHERE {?item wdt:P279 wd:Q28823 ; SERVICE wikibase:label {bd:serviceParam wikibase:language "[AUTO_LANGUAGE],mul,en" }} LIMIT 45}FILTER lang(?itemLabel)}',
  'Scifi TV Shows': 'SELECT ?itemLabel {{SELECT DISTINCT ?itemLabel WHERE {?item wdt:P136 wd:Q336059; SERVICE wikibase:label {bd:serviceParam wikibase:language "[AUTO_LANGUAGE],mul,en" }} LIMIT 45} FILTER lang(?itemLabel)}',
  // 'Keyboard Characters': '',  // only 41 items : SELECT ?itemLabel {{SELECT DISTINCT ?itemLabel WHERE {?item wdt:P31 wd:Q27884930; SERVICE wikibase:label {bd:serviceParam wikibase:language "[AUTO_LANGUAGE],mul,en" }} LIMIT 45} FILTER lang(?itemLabel)}
  'Weather Words': 'SELECT ?itemLabel {{SELECT DISTINCT ?itemLabel WHERE {VALUES ?types {wd:Q16332653 wd:Q118733587} ?item wdt:P31 ?types; SERVICE wikibase:label {bd:serviceParam wikibase:language "[AUTO_LANGUAGE],mul,en" }} LIMIT 56} FILTER lang(?itemLabel)}',
  // 'Inventions': '',  // wikidata's instances of inventions are low quality data
  'Musicals': 'SELECT ?itemLabel {{SELECT DISTINCT ?itemLabel WHERE {?item wdt:P7937 wd:Q2743; SERVICE wikibase:label {bd:serviceParam wikibase:language "[AUTO_LANGUAGE],mul,en" }} LIMIT 54} FILTER lang(?itemLabel)}',
  'Legal Doctrines': 'SELECT ?itemLabel {{SELECT DISTINCT ?itemLabel WHERE {?item wdt:P31 wd:Q1192543; SERVICE wikibase:label {bd:serviceParam wikibase:language "[AUTO_LANGUAGE],mul,en" }} LIMIT 49} FILTER lang(?itemLabel)}',
  'Poets': 'SELECT ?itemLabel {{SELECT DISTINCT ?itemLabel WHERE {?item wdt:P106 wd:Q49757; SERVICE wikibase:label {bd:serviceParam wikibase:language "[AUTO_LANGUAGE],mul,en" }} LIMIT 45} FILTER lang(?itemLabel)}'
}
*/ 

const queries = {
  'Elements': 'SELECT ?itemLabel {{SELECT DISTINCT ?itemLabel WHERE {?item wdt:P31 wd:Q11344 ; SERVICE wikibase:label {bd:serviceParam wikibase:language "[AUTO_LANGUAGE],mul,en" }} LIMIT 45}FILTER lang(?itemLabel)}',
  'African Countries': 'SELECT ?itemLabel {{SELECT DISTINCT ?itemLabel WHERE {?item wdt:P31 wd:Q6256 ; wdt:P30 wd:Q15 ; SERVICE wikibase:label {bd:serviceParam wikibase:language "[AUTO_LANGUAGE],mul,en" }} LIMIT 45}FILTER lang(?itemLabel)}',
  'Muppets': 'SELECT ?itemLabel {{SELECT DISTINCT ?itemLabel WHERE {VALUES ?types {wd:Q2120540 wd:Q155629} ?item wdt:P1441 ?types; SERVICE wikibase:label {bd:serviceParam wikibase:language "[AUTO_LANGUAGE],mul,en" }} LIMIT 45}FILTER lang(?itemLabel)}',
  'Internet Memes': 'SELECT ?itemLabel {{SELECT DISTINCT ?itemLabel WHERE {?item wdt:P31 wd:Q2927074 ; SERVICE wikibase:label {bd:serviceParam wikibase:language "[AUTO_LANGUAGE],mul,en" }} LIMIT 45}FILTER lang(?itemLabel)}',
  'Musical Instruments': 'SELECT ?itemLabel {{SELECT DISTINCT ?itemLabel WHERE {?item wdt:P31 wd:Q110295396 ; SERVICE wikibase:label {bd:serviceParam wikibase:language "[AUTO_LANGUAGE],mul,en" }} LIMIT 45}FILTER lang(?itemLabel)}',
}

const items = ref([])
const score = ref(0)
const mistakes = ref(0)
const group_amount = Object.keys(queries).length
const group_size = 45

function shuffle(array) {
  // from https://stackoverflow.com/a/12646864
  for (let i = array.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [array[i], array[j]] = [array[j], array[i]];
  }
}

const selectedItem = ref()

function deselect() {
  selectedItem.value = null
}

function selectItem(item) {
  if (selectedItem.value == null) { // first item
    selectedItem.value = item
  } else if (item === selectedItem.value) { // select same item -> deselect
    deselect()
  } else { // 2 items selected
    if (item.category === selectedItem.value.category) { // both items in the same category
      score.value++
      item.names.push(...selectedItem.value.names)

      const indexToRemove = items.value.indexOf(selectedItem.value)
      if (indexToRemove > -1) {
        items.value.splice(indexToRemove, 1)
      }
    } else { // both items in different categories
      mistakes.value++
    }
    deselect()
  }
}

async function loadData() {
  try {
    const fetchPromises = Object.entries(queries).map(async ([key, query]) => {

      const url = `${API_URL}${encodeURIComponent(query)}`

      const response = await fetch(url)
      if (!response.ok) throw new Error(`HTTP error! status: ${response.status}`)

      const data = await response.json()

      console.log(data.results.bindings)

      return data.results.bindings.map(item => ({
        id: item.itemLabel.value,
        category: key,
        names: [item.itemLabel.value[0].toUpperCase() + item.itemLabel.value.slice(1)]
      }))
    })
    const results = await Promise.all(fetchPromises)
    items.value = results.flat()

    shuffle(items.value)

  } catch (error) {
    console.error('Failed to fetch data from Wikidata:', error)
  }
}

onMounted(() => {
  loadData()
})
</script>

<template>
  <p>Make {{ group_amount }} groups of {{ group_size }}! (By combining two at a time.) Score: {{ score }} Mistakes: {{ mistakes }} </p>

  <div v-if="items.length > 0">
    <span v-for="item in items" :key="item.id">
      <button class="item-button" :class="{
        'selected': item === selectedItem,
        'bold': item.names.length > 1
      }" :title="item.names.join('\n')" :disabled="item.names.length >= group_size"
        @click="selectItem(item)">
        <template v-if="item.names.length >= group_size">
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