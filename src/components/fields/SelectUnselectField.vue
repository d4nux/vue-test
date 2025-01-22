<template>
    <div class="select-container">
        <div class="campos">
            <label>{{ availableLabel }}</label>
            <select multiple
                :name="field.id"
                :disabled="field.disabled ?? null"
                @change="moveToDisabled"
            >
                <option
                    v-for="option in availableOptions"
                    :key="option.id"
                    :value="option.id"
                >
                    {{ option.label }}
                </option>
            </select>
        </div>
        <div class="campos">
            <label>{{ disabledLabel }}</label>
            <select multiple
                :name="field.id"
                :disabled="field.disabled ?? null"
                @change="moveToAvailable"
            >
                <option
                    v-for="option in disabledOptions"
                    :key="option.id"
                    :value="option.id"
                >
                    {{ option.label }}
                </option>
            </select>
        </div>
    </div>
</template>

<script setup>
import { ref } from 'vue';
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

const availableOptions = ref(props.field.options);
const disabledOptions = ref([]);
const availableLabel = ref('Available Options');
const disabledLabel = ref('Disabled Options');

const moveToDisabled = (event) => {
    const selectedOptions = Array.from(event.target.selectedOptions, option => option.value);
    disabledOptions.value.push(...availableOptions.value.filter(option => selectedOptions.includes(option.id)));
    availableOptions.value = availableOptions.value.filter(option => !selectedOptions.includes(option.id));
    updateLabels();
};

const moveToAvailable = (event) => {
    const selectedOptions = Array.from(event.target.selectedOptions, option => option.value);
    availableOptions.value.push(...disabledOptions.value.filter(option => selectedOptions.includes(option.id)));
    disabledOptions.value = disabledOptions.value.filter(option => !selectedOptions.includes(option.id));
    updateLabels();
};

const updateLabels = () => {
    availableLabel.value = availableOptions.value.length ? 'Available Options' : 'Disabled Options';
    disabledLabel.value = disabledOptions.value.length ? 'Disabled Options' : 'Available Options';
};
</script>

<style lang="css" scoped>
    .select-container {
        display: flex;
        flex-direction: row;
        gap: 10px; /* Espacio entre los select */
        width: 100%;
    }
    .campos {
        flex: 1;
        margin-bottom: 10px;
    }

    select {
        background: black;
        border-width: 1px;
        padding: 10px;
        height: 100px;
        width: 100%; /* Ampliar el tamaño de los select */
        /* Quitar la flecha del select */
        -webkit-appearance: none;
        -moz-appearance: none;
        appearance: none;
    }
</style>