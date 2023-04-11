<template>
<main>
    <h1>Create ToDo</h1>
    <ToDoCreator @create-todo="createTodo" />
</main>
</template>

<script>
import {
    ref
} from "vue";
import {
    uid
} from 'uid';
import ToDoCreator from '../components/ToDoCreator.vue';

const todoList = ref([]);

const createTodo = (todo) => {
    todoList.value.push({
        id: uid(),
        todo: todo,
        isCompleted: null,
        isEditing: null,
    });
};

export default {
    name: 'ToDosView',
    components: {
        ToDoCreator
    },
    setup() {
        return {
            todoList,
            createTodo
        };
    }
};
</script>

<style lang="scss" scoped>
main {
    display: flex;
    flex-direction: column;
    max-width: 500px;
    width: 100%;
    margin: 0 auto;
    padding: 40px 16px;

    h1 {
        margin-bottom: 16px;
        text-align: center;
    }
}
</style>
