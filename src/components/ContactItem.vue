<template>
  <li class="flex items-center justify-between mb-4 p-2 border-b border-gray-200 rounded-lg">
    <div class="flex flex-col">
      <span class="font-medium text-gray-800">{{ contact.name }}</span>
      <span class="text-sm text-gray-600">{{ contact.phone }}</span>
      <span class="text-sm text-gray-600">{{ contact.email }}</span>
    </div>
    <div class="flex space-x-2">
      <button
          @click="$emit('editContact', contact)"
          class="bg-yellow-400 text-white px-4 py-2 rounded-lg hover:bg-yellow-500"
      >
        Редактировать
      </button>
      <button
          @click="$emit('deleteContact', contact.id)"
          class="bg-red-500 text-white px-4 py-2 rounded-lg hover:bg-red-600"
      >
        Удалить
      </button>
    </div>
  </li>
</template>

<script lang="ts">
import { defineComponent, PropType } from 'vue';
import { Contact } from '../types';

export default defineComponent({
  props: {
    contact: {
      type: Object as PropType<Contact>,
      required: true,
    },
  },
});
</script>
