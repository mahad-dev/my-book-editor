<script setup>
import { ref, onMounted, defineProps, watch } from "vue";
import axios from "axios";
import vSelect from "vue-select";
import "vue-select/dist/vue-select.css";

const props = defineProps(["emitSelectedBook", "selectedBook"]);
const options = ref([]);
const selectedBook = ref(null);

onMounted(async () => {
  if (props.selectedBook) selectedBook.value = props.selectedBook;
  try {
    const response = await axios.get(
      "https://api.nytimes.com/svc/books/v3/lists/hardcover-fiction.json",
      {
        params: {
          "api-key": import.meta.env.VITE_API_KEY, // Replace with your actual API key
        },
      }
    );
    // Assuming the data structure is similar to your example
    options.value = response.data.results.books.slice(5, 10).map((book) => ({
      id: book.primary_isbn13,
      title: book.title,
      author: book.author,
    }));
    console.log(response);
  } catch (error) {
    console.error("Error fetching data:", error);
  }
});

watch(selectedBook, (newValue) => {
  props.emitSelectedBook(newValue);
});
</script>

<template>
  <div class="max-w-[350px]">
    <h2 class="mb-5">Select the Book to edit cover page</h2>
    <v-select v-model="selectedBook" :options="options" label="title" />
  </div>
</template>
