<!-- PhotoUploader.vue -->
<template>
  <main class="min-h-svh flex flex-col items-center justify-center p-4">
    <div class="grid gap-4 max-w-3xl container">
      <a href="https://kos0616.github.io/photoData/IMG_7936.jpeg" target="_blank">
        <img src="/IMG_7936.jpeg" class="max-w-[4rem]" />
        下載範例圖片
      </a>
      <label class="p-4 border rounded-xl block">
        <input type="file" accept="image/*" @change="handleFile" />
        <div v-if="imageDataUrl" class="mt-4">
          <strong>拍攝時間： {{ dateInfo || "無資料" }}</strong>
          <img :src="imageDataUrl" class="max-w-md rounded-xl" />
        </div>
      </label>
      <div class="border p-4 rounded-xl min-w-md">
        <strong class="text-2xl">ALL EXIF</strong>
        <table v-if="exifInfo" class="mt-4">
          <tr v-for="(value, key) in exifInfo" :key="key">
            <th class="font-bold text-end px-2">{{ key }}:</th>
            <td>{{ value }}</td>
          </tr>
        </table>
      </div>
    </div>
  </main>
</template>

<script lang="ts" setup>
import { ref } from "vue";
import EXIF from "exif-js";

const imageDataUrl = ref<string | null>(null);
const exifInfo = ref<Record<string, unknown> | null>(null);
const dateInfo = ref<string | null>(null);

function handleFile(event: Event) {
  const input = event.target as HTMLInputElement;
  if (!input.files || !input.files[0]) return;

  const file = input.files[0];
  const reader = new FileReader();

  EXIF.getData(file, function () {
    const allExif = EXIF.getAllTags(this);

    exifInfo.value = allExif;
    dateInfo.value = EXIF.getTag(this, "DateTime");
  });

  reader.onload = function (e) {
    const result = e.target?.result as string;
    imageDataUrl.value = result;

    // const image = new Image();
    // image.onload = () => {
    //   EXIF.getData(image, function () {
    //     console.log(image);

    //     const allExif = EXIF.getAllTags(this);
    //     console.log(allExif);

    //     exifInfo.value = allExif;
    //   });
    // };
    // image.src = result;
  };

  reader.readAsDataURL(file);
}
</script>
