<script setup>
import { ref, reactive } from 'vue'
import { createClient } from 'pexels'
import { onMounted } from 'vue'
import { watch, watchEffect } from 'vue'
const client = createClient('9OVbR8F7wnzyrUlBiMZrdAIzTdzg8P56V5nmccHdrtebSDEdTUbgxfIZ');
let currentPhotoIndex = -1;
const prevButtonDisabled = ref(true)
const nextButtonDisabled = ref(true)
const collection = reactive({
  media: {},
})
const photo = reactive({
  title: "",
  photographer: "",
  photographer_url: "",
  url: ""
})

watch(() => { return collection.media },
  (newValue, oldValue) => {
    console.log("collection changed")
    console.log(newValue)
    if(newValue.length > 0) {
      loadImage(1)
    }  
  }
)

async function loadImage(op) {
  console.log(op)
  let nextPhotoIndex = currentPhotoIndex + op;
  console.log("next index: " + nextPhotoIndex)
  if (nextPhotoIndex < 0 || nextPhotoIndex >= collection.media.length) {
    return
  }
  currentPhotoIndex = nextPhotoIndex
  prevButtonDisabled.value = ((currentPhotoIndex - 1) >= 0) ? false : true
  nextButtonDisabled.value = ((currentPhotoIndex + 1) < collection.media.length) ? false : true
  console.log("getting photo from cache")
  console.log(collection.media[currentPhotoIndex])
  photo.title = collection.media[currentPhotoIndex].alt
  photo.photographer = collection.media[currentPhotoIndex].photographer
  photo.photographer_url = collection.media[currentPhotoIndex].photographer_url
  photo.url = collection.media[currentPhotoIndex].src.tiny
  console.log(photo)
}
async function fetchCollection() {
 
  let url = "https://api.pexels.com/v1/collections?page1"
  const options = {
    method: "GET",
    headers: {
      "Authorization": "Put API key here",
    }
  }
  let response = await fetch(url, options)
  if (response.status == 200) {
    response = await response.json()
    console.log(response)
  }
  else {
    console.log("Cannot fetch collections")
    return
  }
  const id = response.collections[0].id
  console.log(id)
  // Get media from collection
  url = `https://api.pexels.com/v1/collections/${id}?type=photos&page=1&per_page=15`
  console.log(url)
  // use same options as above
  response = await fetch(url, options)
  if (response.status == 200) {
    const data = await response.json()
    console.log(data)
    collection.media = data.media  // save photos
  }
  else {
    console.log("Cannot fetch photos")
  }
}
onMounted(() => {
  console.log("onmounted")
  fetchCollection()
})
</script>

<template>
  
  <div id="slideshow">

    <div id="controls">
      <div id="photo">
        <img :src="photo.url">
      </div>
    </div>

    <div id="photo-info"> 

      <button type="button" @click="loadImage(-1)" :disabled="prevButtonDisabled">
        <span class="material-icons">Previous</span>
      </button>

      <button type="button" @click="loadImage(1)" :disabled="nextButtonDisabled">
        <span class="material-icons">Next</span>
      </button><br>

      <span>{{ photo.title }}</span><br>
      <span>Photographer:
        <a :href="photo.photographer_url" target="_blank">
          {{ photo.photographer }}
        </a></span>
    </div>

    <div id="source">
      <span>Images provided by </span>
      <a href="http://pexels.com">
        <span>Pexels.com</span>
      </a>
      
    </div>
  </div>
</template>

<style>
body{
  background-color:beige
}
#slideshow {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 50px;
}
#controls {
  display: flex;
  align-items: center;
}
button {
  border: 20;
  background-color: white;
  padding: 5;
  font-size: 15px;
}
#photo img {
  border-radius: 100px;
}
#photo-info,
#photo img {
  width: 400px;
}
#photo-info {
  font-family: Verdana, Geneva, Tahoma, sans-serif;
  padding: 20px 20px;
  text-align: center;
}
#photo-info span {
  display: inline-block;
  margin-top:1px;
}
#source img {
  width: 30px;
  margin-right: 5px;
}
</style>