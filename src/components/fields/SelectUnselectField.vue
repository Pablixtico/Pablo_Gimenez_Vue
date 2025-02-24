<template>
    <!--Definimos las listas-->
    <div class="lists">
        <!--Definimos la Available options-->
        <div class="list">
            <p>Available Options</p>
            <select multiple 
            name="availableOptions" 
            id="availableOptions"
            >
                <option v-for="option in availableOptions" 
                :key="option.id" 
                :value="option.id"
                @dblclick="moveToDisabled(option)"
                >
                    {{ option.label }}
                </option>
            </select>
        </div>
        <!--Definimos la Disabled options-->
        <div class="list">
            <p>Disabled Options</p>
            <select multiple 
            name="availableOptions" 
            id="availableOptions"
            >
                <option v-for="option in disabledOptions" 
                :key="option.id" 
                :value="option.id"
                @dblclick="moveToAvailable(option)"
                >
                    {{ option.label }}
                </option>
            </select>
        </div>
    </div>
</template>

<script setup>
import { ref, watch } from 'vue';
const emit = defineEmits(['update']);
const props = defineProps({
    field: {
        type: Object,
        required: true,
    },
    modelValue: {
        type: Array,
        default: [],
    },
});

const availableOptions = ref(props.field.options.filter(option => !props.modelValue.includes(option.id)));
const disabledOptions = ref(props.field.options.filter(option => props.modelValue.includes(option.id)));
console.log()

const moveToAvailable = (item) => {
    disabledOptions.value = disabledOptions.value.filter(option => option.id !== item.id);
    availableOptions.value.push(item);
    emitUpdate();
};

const moveToDisabled = (item) => {
    availableOptions.value = availableOptions.value.filter(option => option.id !== item.id);
    disabledOptions.value.push(item);
    emitUpdate();
};

const emitUpdate = () => {
    
    emit('update', {
        id: props.field.id,
        value: disabledOptions.value.map(option => option.id),
    });
};

watch(() => props.modelValue, (newVal) => {
    availableOptions.value = props.field.options.filter(option => !newVal.includes(option.id));
    disabledOptions.value = props.field.options.filter(option => newVal.includes(option.id));
});

</script>

<style scoped>
.lists {
    display: flex;
    justify-content: space-around;
    margin-bottom: 1em;
    width: 100%;
    gap: 2em;
}

.list {
    width: 50%;
}

.list > select {
    width: 100%;
    height: 10em;
    overflow-y: auto;
    padding: 5px;
}
</style>