<template>
  <form>
    <v-card>
      <v-card-title>
        <span class="text-h5">Добавить точку</span>
      </v-card-title>
      <v-card-text>
        <v-text-field
          v-model="pointName"
          :error-messages="pointNameErrors"
          label="Название точки"
          required
          @input="$v.pointName.$touch()"
          @blur="$v.pointName.$touch()"
        ></v-text-field>
        <v-combobox
          v-model="authorName"
          :error-messages="authorNameErrors"
          label="Имя автора"
          placeholder="Если вашего имени нет в списке, можете написать своё."
          required
          clearable
          @input="$v.authorName.$touch()"
          @blur="$v.authorName.$touch()"
          :items="authors"
        ></v-combobox>
      </v-card-text>
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
import { required } from "vuelidate/lib/validators";

export default {
  mixins: [validationMixin],
  props: {
    closeDialog: {
      type: Function,
    },
    createPoint: {
      type: Function,
    },
  },

  validations: {
    pointName: { required },
    authorName: { required },
  },

  data: () => ({
    pointName: "",
    authorName: "",
    authors: ["Шербол", "Бекжан", "Эржан", "Адилет"],
  }),

  computed: {
    pointNameErrors() {
      const errors = [];
      if (!this.$v.pointName.$dirty) return errors;
      !this.$v.pointName.required &&
        errors.push('Поле "Название точки" не должно быть пустым!');
      return errors;
    },
    authorNameErrors() {
      const errors = [];
      if (!this.$v.authorName.$dirty) return errors;
      !this.$v.authorName.required &&
        errors.push('Поле "Имя автора" не должно быть пустым!');
      return errors;
    },
  },

  mounted() {},

  methods: {
    async save() {
      this.$v.$touch();
      if (!this.$v.$invalid) {
        await this.createPoint({
          pointName: this.pointName,
          authorName: this.authorName,
        });
        this.close();
      }
    },
    close() {
      this.$v.$reset();
      this.pointName = "";
      this.authorName = "";
      this.closeDialog();
    },
  },
};
</script>
