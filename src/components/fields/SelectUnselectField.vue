<template>
    <div class="select-container">
        <select
            :name="field.id"
            :disabled="field.disabled ?? null"
            @change="updateData"
        >
            <option
                v-for="option in field.options"
                :key="option.id"
                :value="option.id"
            >
                {{ option.label }}
            </option>
        </select>
        <select
            :name="field.id"
            :disabled="field.disabled ?? null"
            @change="updateData"
        >
            <option
                v-for="option in field.options"
                :key="option.id"
                :value="option.id"
            >
                {{ option.label }}
            </option>
        </select>
    </div>
</template>

<script setup>
import fieldMixin from '../FieldMixin';
const emit = defineEmits(['update']);
const props = defineProps({
    field: {
        type: Object,
        required: true,
    },
    modelValue: {
        type: [String, Number, Boolean, Object, Array],
        default: '',
    },
});
let { handleChange, debounce } = fieldMixin.setup(props, { emit });
const updateData = (event) => {
    debounce(handleChange, 50)(event);
    console.log(event.target.value);
};
</script>

<style lang="css" scoped>
.select-container {
    display: flex;
    flex-direction: row;
    gap: 10px; /* Espacio entre los select */
}

select {
    background: black;
    border-width: 1px;
    padding: 10px;
    height: 100px;
    /* Quitar la flecha del select */
    -webkit-appearance: none;
    -moz-appearance: none;
    appearance: none;
}

option{
    background: black;
    color: white;
    width: 50%;
}
</style>