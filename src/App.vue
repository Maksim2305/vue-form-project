<template>
  <div id="app">
    <UserForm @add-user="addUser" />
    <DataTable :users="users" @delete-user="deleteUser" />
  </div>
</template>

<script>
import UserForm from "./components/UserForm.vue";
import DataTable from "./components/DataTable.vue";

export default {
  name: "App",
  components: {
    UserForm,
    DataTable,
  },
  data() {
    return {
      users: [],
    };
  },
  methods: {
    addUser(user) {
      if (this.users.some((item) => item.id === user.id)) {
        return;
      }
      this.users.push(user);
    },
    deleteUser(user) {
      this.users = this.users.filter((item) => item.id !== user.id);
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>
