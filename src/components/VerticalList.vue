<script setup>
import { ref, onMounted, onUnmounted, onBeforeUnmount } from 'vue'
import { getRandomNumber } from '../utils/getRandomNumber'
import HorizontalList from './HorizontalList.vue'

const AVG_HORIZONTAL_LIST_HEIGH = 131
const EXTRA_RENDER_COUNT = 3 // Extra render count needeed for exclude unnecessary run loadItems
const FIRST_RENDER_COUNT =
  Math.floor((window.innerHeight - 50) / AVG_HORIZONTAL_LIST_HEIGH) + EXTRA_RENDER_COUNT

const items = ref([])
const scrollTriggerRef = ref(null)
const containerRef = ref(null)
const loading = ref(false)
let observer = null

const handleObserver = (entries) => {
  if (entries[0].isIntersecting && !loading.value) {
    loadItems()
  }
}

const loadItems = () => {
  loading.value = true
  const isFirstLoad = items.value.length === 0 ? FIRST_RENDER_COUNT : 10
  // Pushing new items
  for (let i = 0; i < isFirstLoad; i++) {
    items.value.push(`Item ${items.value.length + 1}`)
  }
  loading.value = false
}

onMounted(() => {
  observer = new IntersectionObserver(handleObserver, {
    root: null,
    rootMargin: '0px',
    threshold: 0.1 // Trigger when 10% of the target is visible
  })
  observer.observe(scrollTriggerRef.value)
  loadItems()
})

onUnmounted(() => {
  if (observer) {
    observer.disconnect()
  }
})

let intervalId = null

const startUpdating = () => {
  intervalId = setInterval(updateRandomItem, 1000)
}

const updateRandomItem = () => {
  const els = containerRef.value.querySelectorAll('.horizontal-item[data-visible="true"]')
  const randomIndex = Math.floor(Math.random() * els.length)
  const el = els[randomIndex]

  el.style.transform = 'scale(1.3)'
  el.style.backgroundColor = '#f0f0f0'
  el.style.fontWeight = 'bold'

  setTimeout(() => {
    el.style.transform = ''
    el.style.backgroundColor = ''
    el.style.fontWeight = 'normal'
  }, 1000)

  setTimeout(() => {
    el.textContent = getRandomNumber(0, 100)
  }, 500)
}

onMounted(startUpdating)
onBeforeUnmount(() => clearInterval(intervalId))
</script>

<template>
  <div class="container" ref="containerRef">
    <div class="header">
      Count of vertical elements rendered:&nbsp<b>{{ items.length }}</b>
    </div>
    <ul>
      <li class="vertical-item" v-for="(item, index) in items" :key="index">
        {{ item }}
        <HorizontalList />
      </li>
      <div ref="scrollTriggerRef"></div>
    </ul>
  </div>
</template>

<style scoped>
.container {
  padding-top: 50px;
}
.vertical-item {
  padding: 10px;
  display: flex;
  flex-direction: column;
  gap: 10px;
}
.header {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 50px;
  background-color: #f0f0f0;
  padding: 10px;
  z-index: 999;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 20px;
}
</style>
