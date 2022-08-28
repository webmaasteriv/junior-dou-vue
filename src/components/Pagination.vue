<template>
    <div>
        <ul class="pagination">
            <li v-for="n in totalPages" :key="n" :class="currPage == n?'page-item active':'page-item'">
                <a class="page-link" href="#" @click="$emit('showPage', n)" :data-num="n">{{n}}</a>
            </li>
        </ul>
    </div>
</template>

<script setup>
import {onUpdated} from 'vue';
const props = defineProps({
    currPage:{
        type:Number,
        default:1,
    },
    totalPages:{
        type:Number,
        default:1,
    }
});

console.log('props', props.totalPages);

defineEmits(['showPage']);


</script>

<style scoped>

</style>