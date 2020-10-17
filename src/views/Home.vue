<template>
  <div class="min-h-screen flex items-center justify-center">
    <form class="p-8 w-1/2 bg-white rounded shadow"
          @submit.prevent="handleSubmit">

      <div v-if="message"
           class="bg-green-600 text-white rounded p-4 mb-8">{{ message }}</div>

      <img v-show="imageUrl"
           :src="imageUrl"
           class="w-48 h-48 object-cover">
      <div>
        <input type="file"
               accept="image/*"
               @change="handleImageSelected">
      </div>

      <button class="mt-8 px-4 py-2 bg-indigo-700 text-white">Submit</button>

      <div class="flex flex-wrap mt-8">
        <img v-for="(image, key) in images"
             :key="key"
             :src="image.image"
             class="w-48 h-46 object-cover mr-4 mb-4 shadow rounded">
      </div>
    </form>
  </div>
</template>

<script>
import axios from "axios";
import { useImageUpload } from "@/composables/useImageUpload.js";
import { ref } from "vue";

export default {
  name: "Home",

  setup() {
    let message = ref("");
    let images = ref([]);

    let { imageFile, imageUrl, handleImageSelected } = useImageUpload();

    function handleSubmit() {
      let data = new FormData();
      data.append("image", imageFile.value);

      axios
        .post("http://127.0.0.1:8000/api/upload-image", data)
        .then((response) => {
          imageUrl.value = "";
          imageFile.value = "";
          message.value = response.data.message;
          images.value = response.data.images;
        });
    }

    return {
      handleImageSelected,
      imageUrl,
      handleSubmit,
      message,
      images,
    };
  },
};
</script>
