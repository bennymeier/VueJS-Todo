<template>
  <div class="container">
    <h1>Todos</h1>
    <form @submit.prevent="addTodo">
      <input type="text" placeholder="Add a todo!" v-model="todo.name">
    </form>
    <ul>
      <li v-for="(todo, index) in todos" :key="index">
        {{ todo.data.name }}
        <button @click="deleteTodo(todo.key)" class="rm">Remove</button>
      </li>
    </ul>
    <div class="snackbar" v-if="removed">
      Todo was removed! 🙌🏻
    </div>
  </div>
</template>

<script>
import firebase from "firebase";

export default {
  name: "TodoList",
  data() {
    return {
      removed: false,
      todos: [],
      todo: {
        name: "",
        date: this.getShortDate()
      }
    };
  },
  methods: {
    addTodo() {
      firebase
        .database()
        .ref("todos")
        .push({
          name: this.todo.name,
          date: this.todo.date
        });
      this.todo.name = "";
      this.todo.date = "";
    },
    deleteTodo(index) {
      firebase
        .database()
        .ref(`todos/${index}`)
        .remove()
        .then(() => {
          this.removed = true;
          setTimeout(() => {
            this.removed = false;
          }, 2000);
        })
        .catch(error => {
          console.warn(error);
        });
    },
    getShortDate() {
      return new Date().toLocaleDateString();
    }
  },
  beforeMount() {
    const todos = firebase.database().ref("todos");
    todos.on("value", data => {
      this.todos = [];
      if (data.val()) {
        for (let key in data.val()) {
          const obj = { key: key, data: data.val()[key] };
          this.todos.push(obj);
        }
      }
    });
  }
};
</script>

<style>
body {
  background-color: #f4f4f4;
  text-align: center;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  list-style-type: none;
}
ul li {
  padding: 20px;
  background: white;
  margin-bottom: 8px;
}
input {
  border: none;
  padding: 20px;
  width: calc(50% - 40px);
  box-shadow: 0 5px 5px lightgrey;
  margin-bottom: 50px;
  outline: none;
}
.responsive {
  max-width: 100%;
  height: auto;
}
button {
  padding: 10px;
  min-width: 100px;
  max-width: 100%;
  background: none;
  border: 1px solid lightgray;
  outline: 0;
  cursor: pointer;
}
.rm {
  float: right;
  text-transform: uppercase;
  font-size: 0.8em;
  background: #f9d0e3;
  border: none;
  padding: 5px;
  color: #ff0076;
  cursor: pointer;
}
.snackbar {
  min-width: 250px;
  margin-left: -125px;
  background-color: #333;
  color: #fff;
  text-align: center;
  border-radius: 2px;
  padding: 16px;
  position: fixed;
  z-index: 1;
  left: 50%;
  bottom: 30px;
}
</style>
