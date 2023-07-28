<script setup lang="ts">
import type { Image } from '@/types/models'
import { toRefs } from 'vue'

interface Props {
  selectedImages: Image[]
  deleteSelectedImage: (value: string) => void
}

const props = defineProps<Props>()
const { selectedImages } = toRefs(props)
</script>

<template>
  <section class="list" v-if="selectedImages.length !== 0">
    <h2>List of selected images:</h2>
    <TransitionGroup name="list">
      <div class="list__item" v-for="selectedImg in selectedImages" :key="selectedImg.id">
        <div class="list__item_title">
          <img
            :src="selectedImg.download_url"
            style="width: 50px"
            :alt="`image: ${+selectedImg.id + 1}`"
          />
          <strong>Image - {{ Number(selectedImg.id) + 1 }}</strong>
        </div>

        <a :href="selectedImg.download_url">open in the new tab</a>
        <button class="list__item-delete" @click="deleteSelectedImage(selectedImg.id)">🗙</button>
      </div>
    </TransitionGroup>
  </section>
</template>

<style scoped lang="scss">
.list {
  display: flex;
  flex-direction: column;
  gap: 5px;
  margin: 20px;

  &__item {
    display: flex;
    align-items: center;
    justify-content: space-between;
    border: 1px solid rgba(128, 128, 128, 0.224);
    padding: 10px 5px;
    border-radius: 5px;
    transition: 0.2s;

    &_title {
      display: flex;
      align-items: center;
      gap: 10px;
    }

    &:hover {
      box-shadow: 0 3px 15px rgba(0, 0, 0, 0.24);
    }

    &-delete {
      cursor: pointer;
      transition: 0.2s;
      background: none;
      &:hover {
        color: rgba(201, 50, 50, 0.743);
      }
    }
  }
}

a {
  text-decoration: none;
  color: black;

  &:hover {
    text-decoration: underline;
  }
}

h2 {
  font-size: 22px;
  margin-bottom: 15px;
}

.list-enter-active,
.list-leave-active {
  transition: all 0.5s ease;
}
.list-enter-from,
.list-leave-to {
  opacity: 0;
  transform: translateX(-30px);
}
</style>
