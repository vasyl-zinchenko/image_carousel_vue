<script setup lang="ts">
import { computed, toRefs } from 'vue'
import type { Image } from '../types/models'

interface Props {
  selectedImages: Image[]
  image: Image
  imageWidth: number
  toggleSelection: (value: string) => void
  setIsImageLoader: (value: boolean) => void
}

const props = defineProps<Props>()
const { image, imageWidth, selectedImages } = toRefs(props)

const isSelectedImageExist = computed(() =>
  selectedImages.value.some((img) => img.id === image.value.id)
)
</script>

<template>
  <div class="image-container">
    <img
      @click="toggleSelection(image.id)"
      class="image"
      :style="{ width: imageWidth + 'px' }"
      :src="image.download_url"
      :alt="image.author"
      @load="setIsImageLoader(true)"
    />

    <span class="image__id">{{ Number(image.id) + 1 }}</span>

    <Transition>
      <span v-if="isSelectedImageExist" class="image__checked">✓</span>
    </Transition>
  </div>
</template>

<style scoped lang="scss">
.image-container {
  position: relative;
  display: grid;
}

.image {
  transition: 0.3s;

  &__checked,
  &__id {
    position: absolute;
    display: grid;
    place-items: center;
    height: 40px;
    width: 40px;
    border-radius: 50%;
    font-size: 22px;
    top: 10px;
  }
  &__checked {
    color: greenyellow;
    background-color: rgba(55, 126, 58, 0.252);
    right: 10px;
  }
  &__id {
    background-color: rgba(37, 59, 128, 0.335);
    color: white;
    left: 10px;
  }
}

.v-enter-active,
.v-leave-active {
  transition: opacity 0.2s ease;
}

.v-enter-from,
.v-leave-to {
  opacity: 0;
}
</style>
