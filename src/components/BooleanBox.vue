<template>
    <div class="row g-1 form-inline align-items-baseline">
        <div class="col-3 text-end">
            <label :for="id" class="form-label">{{ label }}</label>
        </div>
        <div class="col-auto">
            <input type="radio" id="radio_no" name="labs_pro" value="false" class="me-2"
                :checked="start === 'false'" 
                @change="$emit('change', value);">
            <label for="radio_no" class="me-2">No</label>
            <input type="radio" id="radio_yes" name="labs_pro" value="true" class="me-2"
                :checked="start === 'true'" 
                @change="$emit('change', value);">
            <label for="radio_yes" class="me-2">Si</label>
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

<style scoped>
.me-2{
    margin: 5px;
}
</style>