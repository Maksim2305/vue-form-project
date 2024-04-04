<template>
  <div class="table-users">
    <table v-if="users.length && render">
      <thead>
        <tr>
          <th>ID клиента</th>
          <th>ФИО</th>
          <th>Дата рождения</th>
          <th>Код специализации</th>
          <th>Важность</th>
          <th>Действия</th>
        </tr>
      </thead>
      <tbody>
        <tr
          v-for="(user, index) in users"
          :key="user.id"
          :class="{ 'row-color': index % 2 }"
        >
          <td>{{ user.id }}</td>
          <td>
            <div class="edit-row">
              <input
                v-if="editableName[user.id]"
                type="text"
                v-model="user.fullName"
              />
              <span v-else>{{ user.fullName }}</span>
              <button @click="editField(user, 'fullName')">
                {{ editableName[user.id] ? "Cохранить" : "Изменить" }}
              </button>
            </div>
          </td>
          <td>{{ user.birthDate }}</td>
          <td>{{ user.code }}</td>
          <td>
            <div class="edit-row">
              <div v-if="!editableImportance[user.id]">
                {{ user.importance }}
              </div>
              <select
                v-else
                @change="changeImportance(user)"
                v-model="selectedImportance"
              >
                <option
                  v-for="importance in importances"
                  :key="importance"
                  :value="importance"
                >
                  {{ importance }}
                </option>
              </select>
              <button @click="editField(user, 'importance')">
                {{ editableImportance[user.id] ? "Cохранить" : "Изменить" }}
              </button>
            </div>
          </td>
          <td>
            <button @click="deleteUser(user)">Удалить</button>
          </td>
        </tr>
      </tbody>
    </table>
    <div v-else>Пользовтелей пока нет</div>
  </div>
</template>

<script>
export default {
  props: ["users"],
  data() {
    return {
      render: true,
      importances: [1, 2, 3],
      selectedImportance: null,
    };
  },
  methods: {
    deleteUser(user) {
      this.$emit("delete-user", user);
    },
    editField(user, field) {
      if (field == "fullName") {
        this.editableName[user.id] = !this.editableName[user.id];
      }
      if (field == "importance") {
        this.editableImportance[user.id] = !this.editableImportance[user.id];
      }
      this.render = false;
      this.$nextTick(() => {
        this.render = true;
      });
    },
    changeImportance(user) {
      user.importance = this.selectedImportance;
    },
  },
  computed: {
    editableName() {
      const obj = {};
      this.users.forEach((user) => {
        obj[user.id] = false;
      });
      return obj;
    },
    editableImportance() {
      const obj = {};
      this.users.forEach((user) => {
        obj[user.id] = false;
      });
      return obj;
    },
  },
};
</script>

<style scoped>
table,
th,
td {
  border: 1px solid;
  padding: 5px 2px;
}
.table-users {
  margin: 20px;
  display: flex;
  align-items: center;
  justify-content: center;
}
table {
  width: 100%;
  border: 1px solid black;
}
.row-color {
  background: #e0e0e0;
}
.edit-row {
  display: flex;
  align-items: center;
  justify-content: space-between;
}
.edit-row img {
  cursor: pointer;
}
</style>
