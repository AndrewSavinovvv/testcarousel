<script setup>
import { defineProps, ref, watch, defineEmits } from 'vue'

const props = defineProps({
  images: {
    type: Array,
    required: true,
  },
})

const currentIndex = ref(0)
const photosToShow = ref([])
const emit = defineEmits(['photo-selected'])

function updatePhotosToShow() {
  photosToShow.value = props.images.slice(currentIndex.value, currentIndex.value + 5)
}

function nextImage() {
  if (currentIndex.value + 5 < props.images.length) {
    currentIndex.value += 1
  } else {
    currentIndex.value = 0
  }
  updatePhotosToShow()
}

function prevImage() {
  if (currentIndex.value > 0) {
    currentIndex.value -= 1
  } else {
    currentIndex.value = props.images.length - 5
  }
  updatePhotosToShow()
}

function selectPhoto(photo) {
  emit('photo-selected', photo)
}

watch(() => props.images, updatePhotosToShow, { immediate: true })
</script>

<template>
  <div class="carousel container">
    <div class="carousel__slides">
      <div
          v-for="photo in photosToShow"
          :key="photo.id"
          class="carousel__slide"
          @click="selectPhoto(photo)"
      >
        <img
            :src="photo.download_url"
            alt="Image"
            class="carousel__image"
        />
      </div>
    </div>
  </div>
  <div class="carousel__btn">
    <button @click="prevImage" class="carousel__button">Prev</button>
    <button @click="nextImage" class="carousel__button">Next</button>
  </div>
</template>

<style scoped>
.carousel {
  display: flex;
  align-items: center;
  justify-content: center;
  position: relative;

}

.carousel__button {
  background-color: #2a6cb8;
  color: white;
  border: none;
  padding: 10px 30px;
  cursor: pointer;
  border-radius: 15px;
  transform: translateY(-50%);
  font-weight: bold;
}

.carousel__button:first-of-type {
  left: 10px;
}

.carousel__button:last-of-type {
  right: 10px;
}

.carousel__slides {
  display: flex;
  overflow: hidden;
  transition: transform 0.5s ease-in-out;
}

.carousel__slide {
  margin: 5px;
  transition: transform 0.3s ease;

}

.carousel__image {
  width: 250px;
  height: 150px;
  border-radius: 10px;
}

.carousel__btn {
  display: flex;
  justify-content: space-evenly;
  margin-top: 40px;
}


@media (min-width: 1305px) {
  .carousel__slide {
    flex: 0 0 20%;
  }
}


@media (max-width: 1304px) and (min-width: 880px) {
  .carousel__slide {
    flex: 0 0 33.33%;
  }
}


@media (max-width: 879px) {
  .carousel__slide {
    flex: 0 0 100%;
  }
  .carousel__image {
    width: 100%;
    height: auto;
  }
  .carousel__slides {
    padding: 10px;
  }
  .carousel__button:first-of-type {
    left: 20px;
  }
}

</style>
