<template>
  <div id="app" class="min-h-screen bg-gray-100 p-4">
    <div class="max-w-4xl mx-auto bg-white p-6 rounded-lg shadow-md">
      <ContactForm
          :contactToEdit="editContactData"
          :addContact="addContact"
          :editContact="editContact"
          class="mb-6"
      />
      <SearchBar
          class="mb-6"
          v-model:searchQuery="searchQuery"
      />
      <div v-if="filteredContacts.length > 0">
        <ContactList
            :contacts="filteredContacts"
            @deleteContact="deleteContact"
            @editContact="handleEditClick"
        />
      </div>
      <div v-else class="text-center text-gray-500">
        Нет совпадений
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, computed, onMounted } from 'vue';
import SearchBar from './components/SearchBar.vue';
import ContactForm from './components/ContactForm.vue';
import ContactList from './components/ContactList.vue';
import { Contact } from './types';

export default defineComponent({
  components: { SearchBar, ContactForm, ContactList },
  setup() {
    const contacts = ref<Contact[]>([]);
    const searchQuery = ref('');
    const editContactData = ref<Contact | null>(null);

    const loadContactsFromLocalStorage = (): Contact[] => {
      const savedContacts = localStorage.getItem('contacts');
      return savedContacts ? JSON.parse(savedContacts) : [];
    };

    const saveContactsToLocalStorage = () => {
      localStorage.setItem('contacts', JSON.stringify(contacts.value));
    };

    const loadContacts = async () => {
      contacts.value = loadContactsFromLocalStorage();
      if (contacts.value.length === 0) {
        try {
          const response = await fetch('/data.json');
          if (!response.ok) throw new Error('Ошибка загрузки данных');
          contacts.value = await response.json();
          saveContactsToLocalStorage();
        } catch (error) {
          console.error('Не удалось загрузить данные:', error);
        }
      }
    };

    const filteredContacts = computed(() => {
      const query = searchQuery.value.toLowerCase();
      return contacts.value.filter(contact =>
          contact.name.toLowerCase().includes(query)
      );
    });

    const handleEditClick = (contact: Contact) => {
      editContactData.value = { ...contact };
    };

    const updateContacts = (updatedContact: Contact) => {
      const index = contacts.value.findIndex(contact => contact.id === updatedContact.id);
      if (index === -1) {
        contacts.value.push(updatedContact);
      } else {
        contacts.value[index] = updatedContact;
      }
      saveContactsToLocalStorage();
      editContactData.value = null;
    };

    const addContact = (newContact: Contact) => {
      updateContacts(newContact);
    };

    const editContact = (updatedContact: Contact) => {
      updateContacts(updatedContact);
    };

    const deleteContact = (id: number) => {
      contacts.value = contacts.value.filter(contact => contact.id !== id);
      saveContactsToLocalStorage();
    };

    onMounted(loadContacts);

    return {
      contacts,
      addContact,
      deleteContact,
      filteredContacts,
      handleEditClick,
      editContactData,
      editContact,
      searchQuery
    };
  },
});
</script>
