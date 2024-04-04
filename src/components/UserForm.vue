<template>
  <div class="user-form" v-if="!loading">
    <div style="padding: 20px">Добавить пользователя</div>
    <form @submit.prevent="hadlerSubmit">
      <div>
        <div class="user-input">
          <input
            type="text"
            v-model="userFullName"
            placeholder="Введите ФИО"
            required
          />
          <div v-if="isNameInvalid" class="error-message">
            ввод только кириллицы, минимум 2 слова
          </div>
        </div>
        <div class="user-input">
          <datepicker
            v-model="userBirthDate"
            placeholder="Введите дату рождения"
            format="dd.MM.yyyy"
            required
          ></datepicker>
          <div v-if="isDateInvalid" class="error-message">
            Дата обязательное поле
          </div>
        </div>
        <div class="user-input">
          <select v-model="userSpecialization" required>
            <option
              v-for="item in specializationsList"
              :key="item.code"
              :value="item"
            >
              {{ item.value }}
            </option>
          </select>
          <div v-if="isSpecializationInvalid" class="error-message">
            Специализация обязательное поле
          </div>
        </div>
      </div>
      <button type="submit">Отправить</button>
    </form>
  </div>
  <div v-else>Загрузка...</div>
</template>

<script>
import axios from "axios";
import datepicker from "vuejs-datepicker";

export default {
  components: {
    datepicker,
  },
  data() {
    return {
      userFullName: "",
      userBirthDate: "",
      userSpecialization: "",
      specializationsList: [],
      isTouchedForm: false,
      loading: false,
    };
  },
  mounted() {
    this.loading = true;
    axios
      .post(
        "https://pp.credit/Services/BrokerService/api/Application/Dictionary",
        { dictName: "ARM_OccupationArea" }
      )
      .then((response) => {
        const { data } = response;
        this.specializationsList = data.description;
      })
      .catch((error) => {
        console.error(error);
      })
      .finally(() => {
        this.loading = false;
      });
  },
  methods: {
    hadlerSubmit() {
      this.isTouchedForm = true;
      if (
        !this.isNameInvalid &&
        !this.isDateInvalid &&
        !this.isSpecializationInvalid
      ) {
        this.$emit("add-user", {
          id: Date.now(),
          fullName: this.userFullName,
          birthDate: this.convertDate(this.userBirthDate),
          specialization: this.userSpecialization.value,
          code: this.userSpecialization.code,
          importance: Math.floor(Math.random() * 3) + 1,
        });
        this.clearForm();
      }
    },
    convertDate(data) {
      let date = new Date(data);
      let year = date.getFullYear();
      let month = date.getMonth() + 1;
      if (month < 10) month = "0" + month;
      let day = date.getDate();
      if (day < 10) day = "0" + day;
      return `${year}.${month}.${day}`;
    },
    clearForm() {
      this.userBirthDate = "";
      this.userSpecialization = "";
      this.userFullName = "";
      this.isTouchedForm = false;
    },
  },
  computed: {
    isNameInvalid() {
      return !/^[А-Яа-яЁё]+\s[А-Яа-яЁё]+$/.test(this.userFullName) &&
        this.isTouchedForm
        ? true
        : false;
    },

    isDateInvalid() {
      return !this.userBirthDate && this.isTouchedForm ? true : false;
    },
    isSpecializationInvalid() {
      return !this.userSpecialization && this.isTouchedForm ? true : false;
    },
  },
};
</script>

<style scoped>
.user-form {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}
.user-input {
  margin-bottom: 40px;
  position: relative;
}
.user-input > input {
  outline: none;
  width: 100%;
}
.error {
  border: 1px solid red !important;
}
.error-message {
  position: absolute;
  bottom: -10px;
  left: 0;
  width: 100%;
  height: 10px;
  color: red;
  font-size: 12px;
  z-index: 1;
}
</style>
