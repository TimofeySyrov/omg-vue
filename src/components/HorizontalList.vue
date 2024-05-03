<script setup>
import { ref, onMounted } from 'vue'
import { getRandomNumber } from '../utils/getRandomNumber'
import { useUUID } from '../utils/useUUID'

const items = ref(
  Array.from({ length: getRandomNumber(10, 30) }, () => ({
    id: useUUID(),
    value: getRandomNumber(10, 30)
  }))
)
const listItemRefs = ref(null)

onMounted(() => {
  const observer = new IntersectionObserver((entries) => {
    entries.forEach((entry) => {
      if (entry.isIntersecting) {
        entry.target.setAttribute('data-visible', 'true')
      } else {
        entry.target.setAttribute('data-visible', 'false')
      }
    })
  })

  listItemRefs.value.forEach((item) => {
    observer.observe(item)
  })
})
</script>

<template>
  <ul>
    <li
      class="horizontal-item"
      v-for="(item, index) in items"
      :key="item.id"
      ref="listItemRefs"
      :data-id="item.id"
    >
      {{ item.value }}
    </li>
  </ul>
</template>

<style scoped>
ul {
  list-style: none;
  display: flex;
  gap: 15px;
  overflow: auto;
}
.horizontal-item {
  min-width: 50px;
  min-height: 50px;
  border-radius: 15px;
  border: 1px solid rgb(0, 0, 0);
  display: flex;
  justify-content: center;
  align-items: center;
  transition: transform 0.3s ease;
  cursor: default;
  margin: 10px 0;
}
.horizontal-item:hover {
  transform: scale(0.8);
}
</style>
