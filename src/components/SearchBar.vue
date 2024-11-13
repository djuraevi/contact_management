<template>
  <div class="flex items-center space-x-2">
    <input
        type="text"
        v-model="localSearchQuery"
        @input="updateSearch"
        class="w-full p-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500"
        placeholder="Поиск по имени"
    />
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, watch } from 'vue';

export default defineComponent({
  props: {
    searchQuery: {
      type: String,
      required: true,
    },
  },
  setup(props, { emit }) {
    const localSearchQuery = ref(props.searchQuery);

    watch(() => props.searchQuery, (newQuery) => {
      localSearchQuery.value = newQuery;
    });

    const updateSearch = () => {
      emit('update:searchQuery', localSearchQuery.value);
    };

    const clearSearch = () => {
      localSearchQuery.value = '';
      emit('updateSearch', '');
    };

    return {
      localSearchQuery,
      updateSearch,
      clearSearch,
    };
  },
});
</script>
