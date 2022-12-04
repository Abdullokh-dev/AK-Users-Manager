<template>
  <div id="app">
    <div class="container-fluid">
      <UserList />
    </div>
  </div>
</template>

<script>
import UserList from './components/UsersList.vue'

export default {
  name: 'App',
  components: {
    UserList
  }
}
</script>

<style>
:root {
  font-family: 'Inter', sans-serif;
  font-weight: 400;
}

body {
  background-color: #F7F7F7;
}
</style>
