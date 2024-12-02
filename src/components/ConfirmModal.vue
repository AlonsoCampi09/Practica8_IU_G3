<template>
    <BaseModal ref="modalRef" id="confirmModal"
      :title="'Confirma accion peligrosa'">
      <template #body>
        <span>{{ text }}</span>
      </template>
      <template #footer>
        <button @click.prevent="() => action()" class="btn btn-primary">
          {{'Confirmar accion' }}
        </button>
      </template>
    </BaseModal>
</template>

<script setup>

import BaseModal from './BaseModal.vue';

import { gState } from '../state.js';
import { ref } from 'vue'

const emit = defineEmits(['add', 'edit'])

const props = defineProps({
  text: String,
  action: Function, // otherwise, editing existing instead of adding
})

let modalRef = ref(null);


// para que el padre pueda llamar a show (hide no debería hacer falta)
function show() {
  modalRef.value.show();
}

function hide() {
  modalRef.value.hide();
}

defineExpose({ show, hide });
</script>

<style scoped>
    .name {
        font-weight: 200%;
    }
</style>