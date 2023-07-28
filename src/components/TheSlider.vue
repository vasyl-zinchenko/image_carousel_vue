<script setup lang="ts">
import { computed, onMounted, onUnmounted, ref, toRefs, watch } from 'vue'
import type { Image } from '../types/models'
import Slide from './SlideItem.vue'
import SelectedItems from './SelectedItems.vue'

interface Props {
  images: Image[]
  isError: boolean
}

const props = defineProps<Props>()
const { images, isError } = toRefs(props)
const selectedImages = ref<Image[]>([])

const isImageLoaded = ref<boolean>(false)
const setIsImageLoader = (value: boolean) => {
  isImageLoaded.value = value
}

const deleteSelectedImage = (id: string) => {
  selectedImages.value = selectedImages.value.filter((image) => image.id !== id)
}

const toggleSelection = (id: string) => {
  const isSelectedImageExist = selectedImages.value.some((image) => image.id === id)
  const selectedImage = images.value.find((image) => image.id === id)
  if (selectedImage && !isSelectedImageExist) {
    selectedImages.value.push(selectedImage)
  } else {
    deleteSelectedImage(id)
  }
}

const windowWidth = ref(window.innerWidth)

const containerWidth = computed(() => {
  if (windowWidth.value >= 1440) {
    return 1420
  } else {
    return windowWidth.value - 20
  }
})

const countOfVisibleImages = computed(() => {
  if (containerWidth.value >= 768 && containerWidth.value < 1024) {
    return 2
  } else if (containerWidth.value >= 1024) {
    return 3
  }
  return 1
})

const imageWidth = computed(() => containerWidth.value / countOfVisibleImages.value)
const start = ref(images.value.length - 1)
const end = ref(4)
const direction = ref('right')

watch(
  images,
  () => {
    start.value = images.value.length - 1
  },
  { immediate: true }
)

const updateWidth = () => {
  windowWidth.value = window.innerWidth
}

onMounted(() => {
  window.addEventListener('resize', updateWidth)
})

onUnmounted(() => {
  window.removeEventListener('resize', updateWidth)
})

const visibleImages = computed(() => {
  if (start.value <= end.value) {
    return images.value.slice(start.value, end.value)
  } else {
    return [...images.value.slice(start.value), ...images.value.slice(0, end.value)]
  }
})

function clickRight() {
  start.value = (start.value + 1) % images.value.length
  end.value = (end.value + 1) % images.value.length
  direction.value = 'right'
}

function clickLeft() {
  start.value = (start.value - 1 + images.value.length) % images.value.length
  end.value = (end.value - 1 + images.value.length) % images.value.length
  direction.value = 'left'
}
</script>

<template>
  <section class="container" :style="{ maxWidth: containerWidth + 'px' }">
    <h1>Image Carousel</h1>

    <section class="carousel">
      <TransitionGroup
        :name="direction"
        class="slide-wrapper"
        :style="{ transform: `translateX(-${imageWidth + 5}px)` }"
        tag="div"
      >
        <Slide
          v-for="image in visibleImages"
          :key="image.id"
          :image="image"
          :toggleSelection="toggleSelection"
          :imageWidth="imageWidth"
          :selectedImages="selectedImages"
          :setIsImageLoader="setIsImageLoader"
        />
      </TransitionGroup>
      <section class="buttons" v-if="isImageLoaded">
        <button class="button buttons__prev" @click="clickLeft">Prev</button>
        <button class="button buttons__next" @click="clickRight">Next</button>
      </section>

      <SelectedItems :selectedImages="selectedImages" :deleteSelectedImage="deleteSelectedImage" />
    </section>
    <p class="error" v-if="isError">Something went wrong!</p>
  </section>
</template>

<style scoped lang="scss">
.error {
  display: flex;
  justify-content: center;
  margin-top: 50px;
  color: red;
}
.container {
  display: flex;
  justify-content: center;
  flex-direction: column;
  overflow: hidden;
  margin: 20px auto;
  transition: all 0.5s ease;
}

.slide-wrapper {
  display: flex;
  position: relative;
  box-sizing: border-box;
  gap: 5px;
}

.buttons {
  display: flex;
  justify-content: center;
  margin-top: 10px;
  display: flex;
  gap: 20px;
}

.responsive {
  width: 100%;
  height: auto;
}

h1 {
  display: flex;
  justify-content: center;
  font-size: 26px;
  margin-bottom: 20px;
}

.right-move,
.right-enter-active,
.right-leave-active,
.left-move,
.left-enter-active,
.left-leave-active {
  transition: all 0.5s ease;
}

.left-enter-from {
  transform: translateX(-100%);
  opacity: 0;
}
.right-leave-to {
  transform: translateX(-100%);
}

.right-leave-active {
  position: absolute;
}

.button {
  appearance: none;
  background-color: #fafbfc;
  border: 1px solid rgba(27, 31, 35, 0.15);
  border-radius: 6px;
  box-shadow:
    rgba(27, 31, 35, 0.04) 0 1px 0,
    rgba(255, 255, 255, 0.25) 0 1px 0 inset;
  box-sizing: border-box;
  color: #24292e;
  cursor: pointer;
  display: inline-block;

  font-size: 14px;
  font-weight: 500;
  line-height: 20px;
  list-style: none;
  padding: 6px 16px;
  position: relative;
  transition: background-color 0.2s cubic-bezier(0.3, 0, 0.5, 1);
  user-select: none;
  -webkit-user-select: none;
  touch-action: manipulation;
  vertical-align: middle;
  white-space: nowrap;
  word-wrap: break-word;
}

.button:hover {
  background-color: #f3f4f6;
  text-decoration: none;
  transition-duration: 0.1s;
}

.button:disabled {
  background-color: #fafbfc;
  border-color: rgba(27, 31, 35, 0.15);
  color: #959da5;
  cursor: default;
}

.button:active {
  background-color: #edeff2;
  box-shadow: rgba(225, 228, 232, 0.2) 0 1px 0 inset;
  transition: none 0s;
}

.button:focus {
  outline: 1px transparent;
}

.button:before {
  display: none;
}

.button:-webkit-details-marker {
  display: none;
}
</style>
