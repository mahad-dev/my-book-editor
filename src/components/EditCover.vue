<script setup>
import { ref } from "vue";

const selectedFile = ref(null);
const selectedFileUrl = ref(null);

const { book, componentIndex, handleComponentIndexChange } = defineProps([
  "book",
  "componentIndex",
  "handleComponentIndexChange",
]);
const selectedText = ref(1);
const arr = ref([
  {
    id: 0,
    text: book.title,
    color: "#000",
    fontSize: 16,
    letterSpacing: 1,
    top: 100,
    left: 12,
  },
  {
    id: 1,
    text: book.author,
    color: "#000",
    fontSize: 16,
    letterSpacing: 1,
    top: 32,
    left: 12,
  },
]);

const handleFileChange = (event) => {
  const file = event.target.files[0];
  if (file) {
    selectedFile.value = file;
    selectedFileUrl.value = URL.createObjectURL(file);
    handleComponentIndexChange(2);
  }
};

const handleMouseDown = (item, event) => {
  event.preventDefault();
  const startX = event.clientX - item.left;
  const startY = event.clientY - item.top;

  const handleMouseMove = (event) => {
    const x = event.clientX - startX;
    const y = event.clientY - startY;

    arr.value = arr.value.map((i) => {
      if (i.id === item.id) {
        i.top = y;
        i.left = x;
      }
      return i;
    });
  };

  const handleMouseUp = () => {
    document.removeEventListener("mousemove", handleMouseMove);
    document.removeEventListener("mouseup", handleMouseUp);
  };

  document.addEventListener("mousemove", handleMouseMove);
  document.addEventListener("mouseup", handleMouseUp);
};

const handleDownloadImage = () => {
  // Create a new canvas element
  const canvas = document.createElement("canvas");
  const ctx = canvas.getContext("2d");

  // Set the canvas size to match the image size
  canvas.width = 370; // Set the width as needed
  canvas.height = 450; // Set the height as needed

  // Draw the image on the canvas with cropping
  const img = new Image();
  img.src = selectedFileUrl.value;

  img.onload = () => {
    const aspectRatio = img.width / img.height;

    // Calculate dimensions for cropping
    let cropWidth = img.width;
    let cropHeight = img.height;

    if (aspectRatio > canvas.width / canvas.height) {
      cropWidth = img.height * (canvas.width / canvas.height);
    } else {
      cropHeight = img.width * (canvas.height / canvas.width);
    }

    // Calculate positioning for cropping (centered)
    const offsetX = (img.width - cropWidth) / 2;
    const offsetY = (img.height - cropHeight) / 2;

    // Draw the cropped image on the canvas
    ctx.drawImage(
      img,
      offsetX,
      offsetY,
      cropWidth,
      cropHeight,
      0,
      0,
      canvas.width,
      canvas.height
    );

    // Draw the text on the canvas
    arr.value.forEach((item) => {
      ctx.fillStyle = item.color;
      ctx.font = `${item.fontSize}px Arial`; // You can change the font family
      ctx.fillText(item.text, item.left + 25, item.top + 25);
    });

    // Trigger the download
    const link = document.createElement("a");
    link.href = canvas.toDataURL("image/png");
    link.download = "modified_image.png";
    link.click();
  };
};
</script>

<template>
  <div class="mt-6 mb-10">
    <label
      v-if="componentIndex === 1"
      class="w-64 flex flex-col items-center px-4 py-6 bg-white text-blue rounded-lg shadow-lg tracking-wide uppercase border border-blue cursor-pointer hover:bg-blue-500 hover:text-white"
    >
      <svg
        class="w-8 h-8"
        fill="currentColor"
        xmlns="http://www.w3.org/2000/svg"
        viewBox="0 0 20 20"
      >
        <path
          d="M16.88 9.1A4 4 0 0 1 16 17H5a5 5 0 0 1-1-9.9V7a3 3 0 0 1 4.52-2.59A4.98 4.98 0 0 1 17 8c0 .38-.04.74-.12 1.1zM11 11h3l-4-4-4 4h3v3h2v-3z"
        />
      </svg>
      <span class="mt-2 text-base leading-normal">Select a file</span>
      <input
        type="file"
        class="hidden"
        ref="fileInput"
        @change="handleFileChange"
      />
    </label>

    <!-- Conditionally render the image -->
    <div
      v-else
      class="grid grid-cols-[.6fr_1fr] items-center mx-auto"
      :class="{ '!grid-cols-1': componentIndex === 3 }"
    >
      <div v-if="componentIndex === 2" class="form_wrap">
        <div class="form_1 data_info">
          <form>
            <div class="form_container">
              <div class="input_wrap">
                <label>Font Size</label>
                <input
                  v-model="arr[selectedText].fontSize"
                  type="number"
                  class="input"
                />
              </div>
              <div class="input_wrap">
                <label for="password">Letter Spacing</label>
                <input
                  v-model="arr[selectedText].letterSpacing"
                  class="input"
                  type="number"
                />
              </div>
              <div class="input_wrap">
                <label for="confirm_password">Text Color</label>
                <input
                  v-model="arr[selectedText].color"
                  class="input !p-[0px]"
                  type="color"
                />
              </div>
            </div>
          </form>
        </div>
      </div>
      <div class="relative overflow-hidden">
        <span
          v-if="componentIndex !== 3"
          v-for="item in arr"
          :key="item.id"
          class="absolute border border-transparent p-1"
          :class="{
            '!border-black': selectedText === item.id && componentIndex !== 3,
          }"
          @click="selectedText = item.id"
          @mousedown="handleMouseDown(item, $event)"
          :style="{
            top: item.top + 'px',
            left: item.left + 'px',
            color: item.color,
            letterSpacing: item.letterSpacing + 'px',
            fontSize: item.fontSize + 'px',
            cursor: componentIndex !== 3 ? 'grab' : 'auto',
          }"
          >{{ item.text }}</span
        >
        <span
          v-else
          v-for="item in arr"
          :key="item.text"
          class="absolute border border-transparent p-1"
          :class="{
            '!border-black': selectedText === item.id && componentIndex !== 3,
          }"
          @click="selectedText = item.id"
          :style="{
            top: item.top + 'px',
            left: item.left + 'px',
            color: item.color,
            letterSpacing: item.letterSpacing + 'px',
            fontSize: item.fontSize + 'px',
            cursor: componentIndex !== 3 ? 'grab' : 'auto',
          }"
          >{{ item.text }}</span
        >
        <div class="h-[450px] w-[370px] mx-auto">
          <img
            :src="
              selectedFileUrl ||
              'https://myersedpress.presswarehouse.com/publishers/default_cover.png'
            "
            alt="Selected Image"
            class="w-full h-full object-cover"
          />
          <h1>Please select image</h1>
        </div>
      </div>
      <div v-if="componentIndex === 3" class="btns_wrap !mt-8">
        <div class="common_btns form_2_btns">
          <button
            @click="handleComponentIndexChange(2)"
            type="button"
            class="btn_back"
          >
            Back
          </button>
          <button
            @click="handleDownloadImage"
            type="button"
            class="btn_next ml-auto"
          >
            Download
          </button>
        </div>
      </div>
    </div>
  </div>
</template>
