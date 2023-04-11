<template>
<div class="input-wrap">
    <input type="text" v-model="todo" />
    <button @click="createTodo">Create</button>
</div>
<ul>

    <li v-for="todoItem in todoList" :key="todoItem.id">
        <span>{{ todoItem.item }}</span>
        <button @click="() => removeTodoItem(todoItem)">Remove</button>

    </li>

</ul>
</template>

<script>
import axios from "axios"
import {
    ref,
    defineEmits,
    watch
} from "vue";
import {
    uid
} from "uid";
import Swal from 'sweetalert2/dist/sweetalert2.js'
import 'sweetalert2/dist/sweetalert2.css'

export default {
    name: "ToDoCreator",
    data() {
        return {
            todoList: [],
            Item: "",

        }

    },
    async mounted() {
        let result = await axios.get("https://localhost:44305/Home/getItems");
        this.todoList = result.data;
        console.log(result.data);

    },
    setup() {
        const emit = defineEmits(["create-todo"]);
        const todo = ref("");
        const todoList = ref(JSON.parse(localStorage.getItem("todoList")) || []);

        const createTodo = async () => {
            const newTodo = todo.value.trim();
            if (!newTodo) {
                Swal.fire({
                    icon: 'error',
                    title: 'Oops...',
                    text: 'Please enter a todo item!',
                });
                return;
            }

            const existingTodo = todoList.value.find(item => item.todo === newTodo);
            if (existingTodo) {
                Swal.fire({
                    icon: 'question',
                    title: `${newTodo} already exists in the list. Do you want to add it again?`,
                    showCancelButton: true,
                    confirmButtonText: 'Yes',
                    cancelButtonText: 'No'
                }).then(async (result) => {
                    if (result.isConfirmed) {
                        const newItem = {
                            item: newTodo,
                        };
                        try {
                            const response = await axios.post('https://localhost:44305/Home/Insert', newItem);
                            console.log(response.data); // Log the response data if needed
                            todoList.value.push(response.data);
                            todo.value = "";
                        } catch (error) {
                            console.error(error);
                        }
                    }
                });
            } else {
                const newItem = {
                    item: newTodo,
                };
                try {
                    const response = await axios.post('https://localhost:44305/Home/Insert', newItem);
                    console.log(response.data); // Log the response data if needed
                    todoList.value.push(response.data);
                    todo.value = "";
                } catch (error) {
                    console.error(error);
                }
            }
        };

        const removeTodoItem = async (todoItem) => {
            const index = todoList.value.findIndex((item) => item.ItemId === todoItem.ItemId);
            if (index !== -1) {
                todoList.value.splice(index, 1);
                console.log(todoItem);
                try {
                    const response = await axios.delete(`https://localhost:44305/Home/DeleteItems/${todoItem.itemId}`);
                    console.log(response.data); // Log the response data if needed
                } catch (error) {
                    console.error(error);
                }
            }
        };

        watch(
            () => todoList.value,
            (newValue) => {
                localStorage.setItem("todoList", JSON.stringify(newValue));
            }, {
                deep: true
            }
        );

        return {
            todo,
            todoList,
            createTodo,
            removeTodoItem,
        };
    },
};
</script>

<style lang="scss" scoped>
.input-wrap {
    display: flex;
    transition: 250ms ease;
    border: 2px solid #41b080;

    &:focus-within {
        box-shadow: 0 -4px 6px -1px rgb(0 0 0 / 0.1),
            0 -2px 4px -2px rgb(0 0 0 / 0.1);
    }

    input {
        width: 100%;
        padding: 8px 6px;
        border: none;

        &:focus {
            outline: none;
        }
    }

    button {
        padding: 8px 16px;
        border: none;
    }
}

li {
    color: #41b080;
    font-weight: bold;
    margin-bottom: 10px;
    padding: 10px;
    border-radius: 5px;
    box-shadow: 0 1px 2px rgba(0, 0, 0, 0.2);
    background-color: #f1f1f1;
    display: flex;
    align-items: center;

    &.is-completed {
        text-decoration: line-through;
        color: #ccc;
    }

    button {
        margin-left: auto;
        background-color: #d33;
        color: #fff;
        border: none;
        border-radius: 5px;
        padding: 5px 10px;
        cursor: pointer;
        transition: background-color 0.2s;
    }

    button:hover {
        background-color: #c00;
    }
}

.completed {
    text-decoration: line-through;
}
</style>
