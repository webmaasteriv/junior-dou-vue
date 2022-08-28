<template>
    <div>
        <select class="form-select form_settings_js" aria-label="Filter list" v-model="selectedFilterList" @change="$emit('changeVal', selectedFilterList)">
            <option v-for="item in filterOptions" :key="item.value" :value="item.value">{{ item.option }}</option>
        </select>
    </div>
</template>

<script setup>
const props = defineProps({
  selectedFilterList:{
    type: Array,
    default: [],
  }
});

const emit = defineEmits(['changeVal']);

const filterOptions = [
  {
    value: 0,
    option: 'By Default',
  },
  {
    value: 1,
    option: 'By Birthday ASC',
  },
  {
    value: 2,
    option: 'By Birthday DESC',
  },
  {
    value: 3,
    option: 'By Last Name DESC',
  },
  {
    value: 4,
    option: 'By Last Name ASC',
  },
];
</script>

<style scoped>

</style>