<template>
  <v-app>
    <v-main>
      <TodoList/>
    </v-main>
  </v-app>
</template>

<script>
import TodoList from './components/TodoList';
// import test from './components/test';
export default {
  name: 'App',
  components: {
    TodoList,
  },
  data: () => ({
    //
  }),
};
</script>
