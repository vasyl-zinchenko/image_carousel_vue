<script setup lang="ts">
import { ref, onMounted } from 'vue'
import Slider from './components/TheSlider.vue'
import type { Image } from './types/models'

const images = ref<Image[]>([])
const isError = ref<boolean>(false)

const getImages = async () => {
  try {
    isError.value = false
    const response = await fetch('https://picsum.photos/v2/list/')
    if (!response.ok) {
      throw new Error('Server returned non-200 status')
    }
    const data = await response.json()
    images.value = data
    images.value.forEach((image) => {
      image.download_url = image.download_url.replace(/(id\/\d*).*/g, '$1/500/300')
    })
  } catch (error) {
    isError.value = true
  }
}

onMounted(() => {
  getImages()
})
</script>

<template>
  <Slider :images="images" :isError="isError" />
</template>

<style scoped></style>
