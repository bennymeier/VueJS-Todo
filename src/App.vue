<template>
  <div id="app">
    <TodoList/>
  </div>
</template>

<script>
import TodoList from "./components/TodoList.vue";

export default {
  name: "app",
  components: {
    TodoList
  }
};
</script>

<style>
@import url("https://fonts.googleapis.com/css?family=Pacifico");
</style>
