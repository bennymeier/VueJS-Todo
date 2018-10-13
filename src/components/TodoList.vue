<template>
  <div class="container">
    <h1>Todos 👇🏻</h1>
    <form @submit.prevent="addTodo">
      <input type="text" placeholder="Add a todo! ⚡️" v-model="todo.name">
    </form>
    <ul>
      <li v-for="(todo, index) in todos" :key="index">
        <p class="formatText">{{ todo.data.name }}</p> <span class="date">{{ todo.data.date }}</span>
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
        name: ""
      }
    };
  },
  methods: {
    addTodo() {
      if (this.todo.name.length > 0) {
        firebase
          .database()
          .ref("todos")
          .push({
            name: this.todo.name,
            date: this.getShortDate()
          });
        this.todo.name = "";
        this.todo.date = "";
      }
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
          throw new Error(error);
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
  font-family: 'Pacifico';
}
.formatText {
  word-break: break-all;
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
  align-items: center;
}
.date {
  position: absolute;
  left: 40px;
  padding-right: 10px;
  font-size: 0.8rem;
}
ul {
  list-style-type: none;
  padding: 0 20px 0 20px;
  margin-bottom: 1rem;
}
li {
  list-style-type: none;
  border-radius: 6px;
}
ul li {
  padding: 20px;
  background: white;
  margin-bottom: 8px;
}
input {
  border: none;
  padding: 20px;
  /* width: calc(50% - 250px); */
  width: 60vw;
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
  position: absolute;
  right: 40px;
  padding-bottom: 50px;
  float: right;
  text-transform: uppercase;
  font-size: 0.8em;
  background: #1abc9c;
  border: none;
  padding: 5px;
  color: white;;
  cursor: pointer;
  margin-top: -11px;
}
.rm:hover {
  background: #1abc9ce8;
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
