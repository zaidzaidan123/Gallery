<template>
  <div>
    <header
      class="flex justify-between px-4 md:px-24 mt-14 border-b pb-3 border-[#9C9C9C]"
    >
      <h1 class="text-xl font-bold py-1">Gallery</h1>
      <label
        for="fileInput"
        class="bg-[#72A8CA] px-6 py-1 rounded-full cursor-pointer"
        >Upload</label
      >
      <input
        id="fileInput"
        type="file"
        @change="handleFileUpload"
        accept="image/*"
        class="hidden"
      />
    </header>
    <div
      class="flex flex-wrap px-4 md:px-28 gap-4 mt-28 md:justify-between lg:justify-start mb-10"
      v-if="uploadedImages.length"
    >
      <div
        v-for="(image, index) in uploadedImages"
        :key="index"
        class="w-full md:w-[291px] h-[520px] relative hover-button-parent"
      >
        <div>
          <img
            :src="image"
            alt="Uploaded Image"
            class="w-full h-full object-fill"
          />
          <button
            @click="deleteImage(index)"
            class="absolute top-2 w-6 h-6 items-center flex justify-center right-2 p-1 text-black transition-opacity opacity-0 duration-300 bg-white rounded-full"
          >
            x
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";

export default defineComponent({
  data() {
    return {
      uploadedImages: [] as string[],
      urlApi:
        "https://vintrackers.buildonlinestaging.com/upload/images/" as string,
    };
  },
  methods: {
    async uploadImageToServer(base64Image: string) {
      try {
        const response = await fetch(this.urlApi, {
          method: "POST",
          headers: {
            "Content-Type": "application/json",
          },
          body: JSON.stringify({ images: [base64Image] }),
        });
        if (response.ok) {
          console.log("Image uploaded successfully.");
        } else {
          throw new Error("Failed to upload image.");
        }
      } catch (error) {
        console.error("Error uploading image:", error);
      }
    },
    async handleFileUpload(event: Event) {
      const target = event.target as HTMLInputElement;
      const files = target.files;
      if (files) {
        const file = files[0];
        if (file.size > 2 * 1024 * 1024) {
          alert("Image size exceeds 2MB limit.");
          return;
        }
        const reader = new FileReader();
        reader.readAsDataURL(file);
        reader.onload = () => {
          if (typeof reader.result === "string") {
            this.uploadedImages.push(reader.result);
            this.uploadImageToServer(reader.result);
          }
        };
      }
    },
    deleteImage(index: number) {
      this.uploadedImages.splice(index, 1);
    },
  },
});
</script>

<style scoped>
.hover-button-parent:hover .opacity-0 {
  opacity: 1;
}
</style>
