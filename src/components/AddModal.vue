<template>
  <div class="modal">
    <div class="modal-content">
      <span class="close" @click="$emit('close')">&times;</span>
      <h2>Добавить организацию</h2>
      <form @submit.prevent="submitForm">
        <div class="form-group">
          <label> Название: </label>
          <input
            v-model="name"
            required
            placeholder="Введите название организации"
          />
        </div>
        <div class="form-group">
          <label> ФИО директора:</label>
          <input
            v-model="director"
            @input="validateDirector"
            required
            placeholder="Иванов Иван Иванович"
          />
        </div>
        <div class="form-group">
          <label> Номер телефона:</label>
          <input
            v-model="phone"
            @input="formatPhone"
            required
            placeholder="+74957556983"
          />
        </div>
        <button type="submit" :disabled="!isFormValid">ОК</button>
      </form>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      name: "",
      director: "",
      phone: "",
      isDirectorValid: true,
      isPhoneValid: true,
    };
  },
  computed: {
    // Проверка валидности формы
    isFormValid() {
      return (
        this.name &&
        this.director &&
        this.phone &&
        this.isDirectorValid &&
        this.isPhoneValid
      );
    },
  },
  methods: {
    // Валидация ФИО директора
    validateDirector() {
      // Проверка на наличие цифр или специальных символов в директоре
      const hasDigitsOrSymbols = /[^а-яА-ЯёЁa-zA-Z\s\-']+/.test(this.director);
      if (hasDigitsOrSymbols) {
        // Удаление всех символов кроме букв, пробелов, дефисов и апострофов
        this.director = this.director.replace(/[^а-яА-ЯёЁa-zA-Z\s\-']/g, "");
      }
      // Разделение ФИО на слова
      const words = this.director.trim().split(/\s+/);
      // Проверка, что ФИО состоит из трех слов
      this.isDirectorValid =
        words.length === 3 && words.every((word) => !!word);
    },

    // Форматирование номера телефона
    formatPhone() {
      if (!this.phone.startsWith("+")) {
        this.phone = "+" + this.phone.replace(/[^\d]/g, "");
      } else {
        this.phone = "+" + this.phone.slice(1).replace(/[^\d]/g, "");
      }
      const phonePattern = /^\+7\d{10}$/;
      this.isPhoneValid = phonePattern.test(this.phone);
    },
    // Отправка формы
    submitForm() {
      if (this.isFormValid) {
        this.$emit("add", {
          name: this.name,
          director: this.director,
          phone: this.phone,
        });
      }
    },
  },
};
</script>

<style scoped>
.modal {
  display: flex;
  justify-content: center;
  align-items: center;
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  z-index: 1000;
}

.modal-content {
  background-color: white;
  padding: 20px;
  border-radius: 4px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
  width: 400px;
  position: relative;
}

.close {
  position: absolute;
  top: 10px;
  right: 10px;
  font-size: 30px;
  cursor: pointer;
}

.close:hover {
  color: red;
}

h2 {
  margin-top: 0;
}

form {
  display: flex;
  flex-direction: column;
}

.form-group {
  display: flex;
  align-items: center;
  margin-bottom: 10px;
}

label {
  width: 120px;
  text-align: center;
}

input {
  flex: 1;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

button {
  padding: 10px;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  margin-top: 10px;
}

button:disabled {
  background-color: #ccc;
  cursor: not-allowed;
}
</style>
