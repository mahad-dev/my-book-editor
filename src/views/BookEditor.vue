<script setup>
import BookDropDown from "../components/BookDropDown.vue";
import EditCover from "../components/EditCover.vue";
import { onUpdated, ref } from "vue";

const timeLine = ["Choose Book", "Edit Cover", "Preview & Download"];
const componentIndex = ref(0);
const selectedBook = ref(null);

const handleSelectedBook = (value) => {
  selectedBook.value = value;
};
const handleComponentIndexChange = (value) => {
  componentIndex.value = value;
};
onUpdated(() => {
  // console.log("a", selectedBook.value);
});
</script>

<template>
  <div class="wrapper flex flex-col items-center">
    <div class="header">
      <ul>
        <li
          v-for="(item, index) in timeLine"
          :key="index"
          class="form_1_progessbar"
          :class="{ active: componentIndex >= index }"
        >
          <div>
            <p>{{ item }}</p>
          </div>
        </li>
      </ul>
    </div>

    <BookDropDown
      v-if="componentIndex === 0"
      :emitSelectedBook="handleSelectedBook"
      :selectedBook="selectedBook"
    />
    <EditCover
      v-if="componentIndex > 0"
      :book="selectedBook"
      :componentIndex="componentIndex"
      :handleComponentIndexChange="handleComponentIndexChange"
    />

    <div v-if="componentIndex !== 3" class="btns_wrap !mt-auto">
      <div class="common_btns form_2_btns">
        <button
          v-if="componentIndex > 0"
          @click="componentIndex--"
          type="button"
          class="btn_back"
        >
          Back
        </button>
        <button
          @click="componentIndex++"
          :disabled="!selectedBook?.id"
          type="button"
          class="btn_next ml-auto disabled:!bg-slate-700 disabled:!cursor-not-allowed"
        >
          Save
        </button>
      </div>
    </div>
  </div>
</template>
