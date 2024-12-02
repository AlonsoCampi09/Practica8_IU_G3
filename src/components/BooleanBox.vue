<template>
    <div class="row g-1 form-inline align-items-baseline">
        <div class="col-3 text-end">
            <label :for="id" class="form-label">{{ label }}</label>
        </div>
        <div class="col-auto">
            <input type="radio" id="radio_no" name="labs_pro" value="false"
                :checked="start === 'false'" 
                @change="$emit('change', value);">
            <label for="radio_no">No</label>
            <input type="radio" id="radio_yes" name="labs_pro" value="true"
                :checked="start === 'true'" 
                @change="$emit('change', value);">
            <label for="radio_yes">Si</label>
        </div>
    </div>
</template>

<script setup>
import { ref, onMounted, defineProps, defineEmits } from 'vue'

const props = defineProps({
    label: String,
    id: String,
    start: String,
})
defineEmits(['change'])

let value = ref(0)

onMounted(() => {
    value.value = props.start;
})

</script>

<style scoped></style>