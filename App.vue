<script>
import {
    RouterView
} from 'vue-router'
import ToDoHeader from './components/ToDoHeader.vue';
import ToDoCreator from './components/ToDoCreator.vue';

export default {
    components: {
        ToDoHeader,
        ToDoCreator
    },
}
</script>

<template>
<div>
    <ToDoHeader />
    <RouterView />
    <!-- <ToDoCreator /> -->
</div>
</template>

<style lang="scss">
@import url('https://fonts.googleapis.com/css2?family=Roboto:wght@100&display=swap');
</style>
