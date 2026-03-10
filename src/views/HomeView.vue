<template>
  <div class="wrap">
    <h1>Vuejs Repositories</h1>

    <div ref="viewArea">
      <div v-for="item in reposData" :key="item.id">
        <div class="my-1 border p-1 rounded-2xl">
          <h3>{{ item.name }}</h3>
          <p>{{ item.description }}</p>
          <a :href="item.html_url" target="_blank">repo url</a>
        </div>
      </div>
    </div>
    <div ref="bottom"></div>
  </div>
</template>

<script setup lang="ts">
import { onMounted, ref } from 'vue'

const reposName = 'vuejs'
const perPage = ref(10)
const page = ref(1)
const bottom = ref(null)
const isLoading = ref(false)

interface ReposData {
  name: string
  id: number
  description: string
  html_url: string
}
const reposData = ref<ReposData[]>([])

const getReposData = async () => {
  if (isLoading.value) return

  isLoading.value = true

  const res = await fetch(
    `https://api.github.com/users/${reposName}/repos?per_page=${perPage.value}&page=${page.value}`,
  )

  const data = await res.json()

  reposData.value.push(...data)

  page.value++

  isLoading.value = false
}

const createObserver = () => {
  const intersectionObserver = new IntersectionObserver(
    (entries) => {
      if (!entries[0]!.isIntersecting) return

      getReposData()
    },
    {
      rootMargin: '100px',
    },
  )

  if (bottom.value) {
    intersectionObserver.observe(bottom.value)
  }
}

onMounted(() => {
  getReposData()
  createObserver()
})
</script>

<style scoped>
.wrap {
  max-width: 500px;
  margin: 0 auto;
}

.border {
  border: 1px solid;
}

.my-1 {
  margin-top: 1rem;
  margin-bottom: 1rem;
}

.p-1 {
  padding: 1rem;
}

.rounded-2xl {
  border-radius: 1rem;
}
</style>
