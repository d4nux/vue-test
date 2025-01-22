<template>
    <div class="select-container">
        <div class="campos">
            <label>{{ availableLabel }}</label>
            <select multiple :name="field.id" :disabled="field.disabled ?? null" @change="moveToDisabled">
                <option v-for="option in availableOptions" :key="option.id" :value="option.id">
                    {{ option.label }}
                </option>
            </select>
        </div>
        <div class="campos">
            <label>{{ disabledLabel }}</label>
            <select multiple :name="field.id" :disabled="field.disabled ?? null" @change="moveToAvailable">
                <option v-for="option in disabledOptions" :key="field.id" :value="field.id">
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

const availableOptions = ref(props.field.options);
const disabledOptions = ref([]);
const availableLabel = ref('Available Options');
const disabledLabel = ref('Disabled Options');

const moveToDisabled = (event) => {
    const selectedOptions = Array.from(event.target.selectedOptions, field => option.value);
    disabledOptions.value.push(...availableOptions.value.filter(field => selectedOptions.includes(field.id)));
    availableOptions.value = availableOptions.value.filter(field => !selectedOptions.includes(field.id));
    updateData(event);
};

const moveToAvailable = (event) => {
    const selectedOptions = Array.from(event.target.selectedOptions, field => field.value);
    availableOptions.value.push(...disabledOptions.value.filter(field => selectedOptions.includes(field.id)));
    disabledOptions.value = disabledOptions.value.filter(field => !selectedOptions.includes(field.id));
    console.log(selectedOptions)
    updateData(event);
};

const updateData = (event) => {
    availableLabel.value = availableOptions.value.length ? 'Available Options' : 'Disabled Options';
    disabledLabel.value = disabledOptions.value.length ? 'Disabled Options' : 'Available Options';
    debounce(handleChange, 50)(event);
    //emit('update', getDisabledOptionsValues());
};

const getDisabledOptionsValues = () => {
    return { options: disabledOptions.value.map(option => option.id) };
};

</script>

<style lang="css" scoped>
.select-container {
    display: flex;
    flex-direction: row;
    gap: 10px;
    width: 100%;
}

.campos {
    flex: 1;
    margin-bottom: 10px;
}

select {
    background: black;
    color: white;
    border-width: 1px;
    padding: 10px;
    height: 100px;
    width: 100%;
    -webkit-appearance: none;
    -moz-appearance: none;
    appearance: none;
}
</style>