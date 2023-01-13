<template>
  <form>
    <v-card>
      <v-card-title>
        <span class="text-h5"
          >{{ isRegister ? "Зарегистрироваться" : "Авторизоваться" }} в Map
          Points</span
        >
      </v-card-title>
      <v-card-text v-if="isRegister">
        <v-text-field
          v-model="name"
          :error-messages="nameErrors"
          label="Имя"
          required
          @input="$v.name.$touch()"
          @blur="$v.name.$touch()"
        ></v-text-field>
        <v-text-field
          v-model="email"
          :error-messages="emailErrors"
          label="Email"
          required
          @input="$v.email.$touch()"
          @blur="$v.email.$touch()"
        ></v-text-field>
        <v-text-field
          v-model="password"
          :error-messages="passwordErrors"
          label="Пароль"
          required
          @input="$v.password.$touch()"
          @blur="$v.password.$touch()"
        ></v-text-field>
        <v-checkbox v-model="checkbox" label="Я продавец"></v-checkbox>
      </v-card-text>
      <v-card-text v-else>
        <v-text-field
          v-model="email"
          :error-messages="emailErrors"
          label="Email"
          required
          @input="$v.email.$touch()"
          @blur="$v.email.$touch()"
        ></v-text-field>
        <v-text-field
          v-model="password"
          :error-messages="passwordErrors"
          label="Пароль"
          required
          @input="$v.password.$touch()"
          @blur="$v.password.$touch()"
        ></v-text-field>
      </v-card-text>

      <v-row no-gutters justify="center">
        <v-btn style="color: white !important; padding: 0" disabled text>
          {{
            isRegister ? "Уже зарегистрированы?" : "Еще не зарегистрированы?"
          }}</v-btn
        >
        <v-btn color="primary" text @click="isRegister = !isRegister">{{
          isRegister ? "Войти" : "Зарегистрироваться"
        }}</v-btn>
      </v-row>

      <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn color="blue darken-1" text @click="close"> Закрыть </v-btn>
        <v-btn color="blue darken-1" text @click="save"> Сохранить </v-btn>
      </v-card-actions>
    </v-card>
  </form>
</template>

<script>
import { validationMixin } from "vuelidate";
import { required, email, minLength } from "vuelidate/lib/validators";

export default {
  mixins: [validationMixin],
  props: {
    closeDialog: {
      type: Function,
      required: true,
    },
    register: {
      type: Function,
      required: true,
    },
    login: {
      type: Function,
      required: true,
    },
  },

  validations: {
    name: { required },
    email: { required, email },
    password: { required, minLength: minLength(6) },
    checkbox: {},
  },

  data: () => ({
    name: "",
    email: "",
    password: "",
    checkbox: false,
    isRegister: true,
  }),

  computed: {
    nameErrors() {
      const errors = [];
      if (!this.$v.name.$dirty) return errors;
      !this.$v.name.required &&
        errors.push('Поле "Имя" не должно быть пустым!');
      return errors;
    },
    emailErrors() {
      const errors = [];
      if (!this.$v.email.$dirty) return errors;
      !this.$v.email.email && errors.push("Неправильный указан email-адрес!");
      !this.$v.email.required &&
        errors.push('Поле "Email" не должно быть пустым!');
      return errors;
    },
    passwordErrors() {
      const errors = [];
      if (!this.$v.password.$dirty) return errors;
      !this.$v.password.required &&
        errors.push('Поле "Пароль" не должно быть пустым!');
      !this.$v.password.minLength &&
        errors.push("Пароль должен состоять минимум из 6 символов!");
      return errors;
    },
  },

  methods: {
    async save() {
      this.$v.$touch();

      const isRegisterValid =
        !this.$v.name.$invalid &&
        !this.$v.email.$invalid &&
        !this.$v.password.$invalid;

      const isLoginValid =
        !this.$v.email.$invalid && !this.$v.password.$invalid;

      if (this.isRegister && isRegisterValid) {
        await this.register({
          name: this.name.trim(),
          email: this.email.trim(),
          password: this.password.trim(),
          isSeller: this.checkbox,
        });
      }
      if (!this.isRegister && isLoginValid) {
        await this.login({
          email: this.email.trim(),
          password: this.password.trim(),
        });
      }

      this.close();
    },
    close() {
      this.$v.$reset();
      this.name = "";
      this.email = "";
      this.password = "";
      this.checkbox = false;
      this.closeDialog();
    },
  },
};
</script>
