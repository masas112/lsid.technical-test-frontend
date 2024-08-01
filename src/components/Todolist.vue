<template>
    <div class="container"><br>
        <h3 align="center">Todolist</h3><br>
        <div class="row">
            <v-table theme="light">
                <thead>
                    <tr>
                        <th class="text-left">id</th>
                        <th class="text-left">Title</th>
                        <th class="text-left">Description</th>
                        <th class="text-center">Status</th>
                        <th class="text-center">Action</th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="todo in result" :key="todo.id">
                        <td>{{ todo.id }}</td>
                        <td>{{ todo.title }}</td>
                        <td>{{ todo.description }}</td>
                        <td class="text-center">{{ todo.status == 1 ? 'Completed' : 'Not Completed'}}</td>
                        <td class="text-center">
                            <v-btn class="mr-2" color="success" size="small" @click="showTodo(todo.id)">Ubah Status</v-btn>
                            <v-btn class="mr-2" color="warning" size="small" @click="editTodo(todo.id)">Edit</v-btn>
                            <v-btn color="danger" size="small" @click="deleteTodo(todo.id)">Delete</v-btn>
                        </td>
                    </tr>
                </tbody>
            </v-table>
        </div>
    </div>
</template>


<script>
import axios from 'redaxios';


export default {
    name: 'Todolist',
    data() {
        return {
            result: {},
            todo: {
                id: '',
                title: '',
                description: '',
                status: ''
            }
        }
    },
    created(){
        this.getIndex();
    },
    methods: {
        getIndex() {
            var page = "http://127.0.0.1:8000/api/index";
            axios.get(page)
            .then(
                ({data})=>{
                    console.log(data);
                    this.result = data;
                }
            )
        }
    }
}
</script>