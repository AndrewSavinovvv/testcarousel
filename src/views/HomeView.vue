<script setup>
import { ref, onMounted, computed } from 'vue'
import CarouselComponent from '@/components/CarouselComponent.vue'

const photos = ref([])
const selectedPhotoIds = ref([])
const isLoading = ref(true)
const error = ref(null)

async function fetchPhotos() {
  isLoading.value = true
  error.value = null
  try {
    const response = await fetch('https://picsum.photos/v2/list?page=1&limit=30')
    photos.value = await response.json()
  } catch (err) {
    error.value = err.message || 'Error fetching photos'
  } finally {
    isLoading.value = false
  }
}

onMounted(() => {
  fetchPhotos()
})


function handlePhotoSelect(photo) {
  const index = selectedPhotoIds.value.indexOf(photo.id)
  if (index === -1) {
    selectedPhotoIds.value.push(photo.id)
  } else {
    selectedPhotoIds.value.splice(index, 1)
  }
}

const selectedPhoto = computed(() => {
  return photos.value.filter((photo) => selectedPhotoIds.value.includes(photo.id))
})
</script>

<template>
  <div>
    <div class="loader" v-if="isLoading"></div>
    <div v-else-if="error">{{ error }}</div>
    <div v-else>
      <CarouselComponent :images="photos" @photo-selected="handlePhotoSelect" />
    </div>
    <div v-if="selectedPhoto.length" class="selected__photos container">
      <h3>Selected images:</h3>
      <ul>
        <li v-for="photo in selectedPhoto" :key="photo.id">
          <a :href="photo.url" target="_blank">{{ photo.url }}</a>
        </li>
      </ul>
    </div>
  </div>
</template>

<style scoped>
.loader {
  border: 5px solid #f3f3f3;
  border-top: 5px solid #3498db;
  border-radius: 50%;
  width: 40px;
  height: 40px;
  animation: spin 1s linear infinite;
  margin: 20px auto;
  z-index: 1;
}
@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}
.selected__photos h3 {
  color: white;
  font-weight: bold;
}
.selected__photos {
  margin-top: 20px;
}
.selected__photos ul,
.selected__photos li {
  list-style: none;
  padding: 0;
  font-size: 16px;
}

.selected__photos a {
  text-decoration: none;
  color: #ffffff;
  font-size: 18px;

}
</style>