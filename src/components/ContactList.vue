<template>
  <div class="bg-white p-4 rounded-lg shadow-lg">
    <h2 class="text-xl font-semibold mb-4">Управление контактами</h2>
    <TransitionGroup name="list" tag="ul">
      <ContactItem
          v-for="contact in reversedContacts"
          :key="contact.id"
          :contact="contact"
          @editContact="$emit('editContact', contact)"
          @deleteContact="$emit('deleteContact', contact.id)"
      />
    </TransitionGroup>
  </div>
</template>

<script lang="ts">
import { defineComponent, PropType } from 'vue';
import ContactItem from './ContactItem.vue';
import { Contact } from '../types';

export default defineComponent({
  components: { ContactItem },
  props: {
    contacts: {
      type: Array as PropType<Contact[]>,
      required: true,
    },
  },
  computed: {
    reversedContacts(): Contact[] {
      return [...this.contacts].reverse();
    }
  },
});
</script>

<style scoped>
.list-enter-active,
.list-leave-active {
  transition: all 0.5s ease;
}
.list-enter-from,
.list-leave-to {
  opacity: 0;
  transform: translateX(30px);
}
</style>
