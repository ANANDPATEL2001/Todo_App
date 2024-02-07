<template>
  <div id="app" class="container">
    <img src="./assets/logo.png" class="logo">
    <TodoList />
  </div>
</template>

<script>
// import HelloWorld from './components/HelloWorld'
import TodoList from './components/TodoList.vue';

export default {
  name: 'App',
  components: {
    TodoList
  }
}
</script>

<style>
* {
  box-sizing: border-box;
}

::-webkit-scrollbar {
  display: none;
}

.container {
  /* max-width: 600px; */
  max-width: 100%;
  margin: 0 auto;
}

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  /* text-align: center; */
  color: #2c3e50;
  margin-top: 60px;
  font-size: 24px;
}

.logo {
  display: block;
  margin: 20px auto;
  height: 75px;
}
</style>
