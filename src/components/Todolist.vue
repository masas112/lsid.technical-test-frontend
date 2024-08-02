<template>
  <div class="container">
    <br />
    <h3 align="center">Todolist</h3>
    <br />
    <div class="row text-center">
      <v-btn class="mr-2" color="success" size="small" @click="dialog = true"
        >Add Todo</v-btn
      >
    </div>
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
            <td class="text-center">
              {{ todo.status == 1 ? "Completed" : "Not Completed" }}
            </td>
            <td class="text-center">
              <v-btn
                class="mr-2"
                color="success"
                size="small"
                @click="ubahStatus(todo)"
                >Ubah Status</v-btn
              >
              <v-btn
                class="mr-2"
                color="warning"
                size="small"
                @click="this.editDialog = true, this.namedialog = 'Edit Todo',
      this.deleteDialog = false,showTodo(todo)"
                >Edit</v-btn
              >
              <v-btn color="danger" size="small" @click="deleteDialog = true, namedialog = 'Delete Todo',showTodo(todo)"
                >Delete</v-btn
              >
            </td>
          </tr>
        </tbody>
      </v-table>
    </div>
  </div>

  <v-dialog
    v-model="dialog"
    height="auto"
    width="1000"
    class="text-center"
    theme="light"
  >
    <v-card>
      <v-card-title class="text-h5">{{ namedialog }}</v-card-title>
      <v-card-text
      v-show="!deleteDialog">
        <v-col>
          <v-col>
            <v-text-field
              class="form-control"
              v-model="todo.title"
              label="Judul"
              required
            ></v-text-field>
          </v-col>

          <v-col>
            <v-text-field
              class="form-control"
              v-model="todo.description"
              label="Deskripsi"
              required
            ></v-text-field>
          </v-col>

          <v-col>
            <v-select
              class="form-control"
              v-model="todo.status"
              :items="['1', '0']"
              label="Status"
              required
            ></v-select>
          </v-col>
        </v-col>

        <small class="text-caption text-medium-emphasis"
          >*indicates required field</small
        >
      </v-card-text>

      <v-divider></v-divider>

      <v-card-actions>
        <v-spacer></v-spacer>

        <v-btn text="Close" variant="plain" @click="dialog = false"></v-btn>

        <v-btn
          color="primary"
          variant="tonal"
          v-show="!deleteDialog"
          @click="!editDialog ? addTodo() : editTodo(todo)"
          >{{ !editDialog ? "Save" : "Update" }}</v-btn
        >
        <v-btn
          color="danger"
          v-show="deleteDialog"
          @click="deleteTodo(todo.id)"
          >Delete</v-btn
        >
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>

<script>
import axios from "redaxios";

export default {
  name: "Todolist",
  data() {
    return {
      result: {},
      dialog: false,
      editDialog: false,
      deleteDialog: false,
      namedialog: "",
      todo: {
        id: "",
        title: "",
        description: "",
        status: "",
      },
    };
  },
  created() {
    this.getIndex();
  },
  methods: {
    getIndex() {
      var page = "http://127.0.0.1:8000/api/index";
      axios.get(page).then(({ data }) => {
        console.log(data);
        this.result = data;
      });
    },
    addTodo() {
      this.editDialog = false;
      this.deleteDialog = false;
      this.namedialog = "Add Todo";
      this.dialog = true;
      console.log(this.todo);
      var page = "http://127.0.0.1:8000/api/store";
      axios.post(page, this.todo).then(({ data }) => {
        console.log(data);
        this.todo = "";
        this.getIndex();
        this.dialog = false
      });
    },
    showTodo(result) {
      console.log(result);
      this.todo = {
        id: result.id,
        title: result.title,
        description: result.description,
        status: result.status,
      }
      this.dialog = true;
      
    },
    deleteTodo(result) {
      this.namedialog = "Delete Todo";
      this.dialog = true;
      this.deleteDialog = true;
      this.editDialog = false;
      console.log(this.todo.id);
      var page = "http://127.0.0.1:8000/api/destroy/" + result;
      axios.delete(page).then(({ data }) => {
        console.log(data);
        this.getIndex();
        this.dialog = false
      });
    },
    editTodo(result) {
      this.namedialog = "Edit Todo";
      this.dialog = true;
      this.editDialog = true;
      console.log(result);
      var page = "http://127.0.0.1:8000/api/update/" + result.id;
      axios.put(page, this.todo).then(({ data }) => {
        console.log(data);
        this.todo = "";
        this.getIndex();
        this.dialog = false;
      });
    },
    ubahStatus(result) {
      console.log(result);
      var page = "http://127.0.0.1:8000/api/update/" + result.id;
      result = {
        title: result.title,
        description: result.description,
        status: result.status == 0 ? 1 : 0
      }
      axios.put(page, result).then(({ data }) => {
        console.log(data);
        this.todo = "";
        this.getIndex();
      });
    }
  },
};
</script>
