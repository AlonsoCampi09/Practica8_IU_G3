<template>
  <div>
    <h4>Profesor <span class="name">{{ user.firstName }} {{ user.lastName }}</span></h4>

    <table>
      <tbody id="table-body">
        <tr>
          <th>Rol</th>
          <td>{{ user.userRole }} </td>
        </tr>
        <tr>
          <th>Créditos</th>
          <td>imparte {{ user.assignedCredits }} de {{ user.maxCredits }}</td>
        </tr>
        <tr>
          <th>Grupos a los que da clase</th>
          <td v-if="user.groups.length">
            {{ user.groups.map(g => formatNiceGroup(g)).join(' ') }}
          </td>
          <td v-else> (ninguno) </td>
        </tr>
      </tbody>
    </table>

    <SortableGrid :data="addSlotCols(slots)" :columns="slotColumns" 
      :filter="{ all: '', fields: [] }" v-model:sorter="sorter" />
    
    <h5>Horarios</h5>

    <!-- Botones para seleccionar el cuatrimestre -->
    <div class="btn-group">
      <button 
        :class="{ active: selectedSemester === 'FALL' }"
        @click="selectedSemester = 'FALL'"
        class="btn btn-outline-primary"
      >
        Primer cuatrimestre
      </button>
      <button 
        :class="{ active: selectedSemester === 'SPRING' }"
        @click="selectedSemester = 'SPRING'"
        class="btn btn-outline-primary"
      >
        Segundo cuatrimestre
      </button>
    </div>

    <!-- Mostrar TimeTable del cuatrimestre seleccionado -->
    <div v-if="selectedSemester === 'FALL'">
      <h6>Primer cuatrimestre</h6>
      <TimeTable :slots="slots.filter(o => o.semester === 'FALL')" />
    </div>
    <div v-else-if="selectedSemester === 'SPRING'">
      <h6>Segundo cuatrimestre</h6>
      <TimeTable :slots="slots.filter(o => o.semester === 'SPRING')" />
    </div>

    <h5>Acciones</h5>
    <div class="btn-group">
      <button @click="$emit('editUser')" title="Editar profesor" class="btn btn-outline-success">✏️</button>
      <button @click="$emit('rmUser')" title="Borrar profesor" class="btn btn-outline-danger">🗑️</button>
    </div>
  </div>
</template>

<script setup>
import SortableGrid from './SortableGrid.vue';
import TimeTable from './TimeTable.vue';

import { gState, semesterNames, weekDayNames } from '../state.js';
import { ref, computed, onMounted, onBeforeUnmount } from 'vue';

defineEmits(['filterUser', 'editUser', 'rmUser']);

const props = defineProps({
  user: Object, // see definition of User in ../model.js
});

let sorter = ref([{ key: 'weekDay', order: 1 }]);
const slots = computed(() => slotsOfAllGroups(props.user.groups));
const selectedSemester = ref('FALL'); // Estado inicial del cuatrimestre seleccionado.

const slotsOfAllGroups = (gg) => {
  const rv = [];
  for (let g of gg.map((o) => gState.resolve(o))) {
    for (let s of g.slots.map((s) => gState.resolve(s))) {
      rv.push(s);
    }
  }
  return rv;
};

const slotColumns = [
  { key: 'niceGroup', display: 'Grupo', type: 'String' },
  {
    key: 'weekDay',
    display: 'Día',
    type: 'Enum',
    values: gState.model.WeekDay,
    displayValues: weekDayNames,
  },
  {
    key: 'semester',
    display: 'Cuatr.',
    type: 'Enum',
    values: gState.model.Semester,
    displayValues: semesterNames,
  },
  { key: 'niceStart', display: 'Inicio', type: 'Time' },
  { key: 'niceEnd', display: 'Final', type: 'Time' },
  { key: 'location', display: 'Lugar', type: 'String' },
];

const addSlotCols = (ss) => {
  for (let s of ss) {
    s.niceGroup = formatNiceGroup(s.groupId);
    s.niceStart = formatTime(s.startTime);
    s.niceEnd = formatTime(s.endTime);
  }
  return ss;
};

// 1450 => "14:50"
const formatTime = (t) => `${Math.floor(t / 100)}:` + `0${t % 100}`.slice(-2);

const formatNiceGroup = (groupId) => {
  const group = gState.resolve(groupId);
  const subject = gState.resolve(group.subjectId);
  return `${subject.short}:${group.name}`;
};

// Event listener for scrolling to "div-details"
onMounted(() => {
  const tableBody = document.getElementById('table-body');
  const details = document.getElementById('div-details');
  if (tableBody && details) {
    tableBody.addEventListener('click', () => {
      details.scrollIntoView({ behavior: 'smooth' });
    });
  }
});

onBeforeUnmount(() => {
  const tableBody = document.getElementById('table-body');
  if (tableBody) {
    tableBody.removeEventListener('click', () => {});
  }
});
</script>

<style>
.btn-group button.active {
  background-color: #007bff;
  color: white;
}
</style>
