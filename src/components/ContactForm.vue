<template>
  <div class="space-y-4">
    <form @submit.prevent="handleSubmit" class="space-y-4">
      <div v-for="(label, field) in fields" :key="field">
        <label :for="field" class="block text-sm font-medium text-gray-700">{{ label }}:</label>
        <input
            v-model="formData[field]"
            :id="field"
            :type="field === 'email' ? 'email' : 'text'"
            class="w-full p-2 border border-gray-300 rounded-md"
            :class="{'input-error': errors[field]}"
            required
        />
        <span v-if="errors[field]" class="error-message">{{ errors[field] }}</span>
      </div>

      <div class="flex justify-between">
        <button
            type="submit"
            class="px-4 py-2 bg-indigo-500 text-white rounded-md hover:bg-indigo-600"
        >
          {{ contactToEdit ? 'Сохранить изменения' : 'Добавить контакт' }}
        </button>
      </div>
    </form>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, watch, PropType } from 'vue';
import { Contact } from '../types';

export default defineComponent({
  props: {
    contactToEdit: {
      type: Object as PropType<Contact | null>,
      default: null,
    },
    addContact: {
      type: Function as PropType<(contact: Contact) => void>,
      required: true,
    },
    editContact: {
      type: Function as PropType<(contact: Contact) => void>,
      required: true,
    },
  },
  setup(props) {
    const fields = {
      name: 'Имя',
      phone: 'Телефон',
      email: 'Email',
    };

    const formData = ref({
      id: 0,
      name: '',
      phone: '',
      email: '',
    });

    const errors = ref<{ [key: string]: string }>({});

    watch(() => props.contactToEdit, (newContact) => {
      if (newContact) {
        formData.value = { ...newContact };
      } else {
        resetForm();
      }
      errors.value = {};
    });

    const validateForm = () => {
      errors.value = {};
      let isValid = true;

      if (!/^\d{7,14}$/.test(formData.value.phone)) {
        errors.value.phone = 'Некорректный формат телефона (7-14 цифр).';
        isValid = false;
      }

      if (!/\S+@\S+\.\S+/.test(formData.value.email)) {
        errors.value.email = 'Некорректный формат email.';
        isValid = false;
      }

      return isValid;
    };

    const resetForm = () => {
      formData.value = { id: 0, name: '', phone: '', email: '' };
    };

    const handleSubmit = () => {
      if (validateForm()) {
        if (props.contactToEdit) {
          props.editContact({ ...formData.value });
        } else {
          props.addContact({ ...formData.value, id: Date.now() });
        }
        resetForm();
      }
    };

    return {
      formData,
      errors,
      handleSubmit,
      fields,
    };
  },
});
</script>

<style scoped>
.input-error {
  border-color: red;
}

.error-message {
  color: red;
  font-size: 12px;
}
</style>
