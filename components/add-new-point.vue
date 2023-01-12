<template>
  <form>
    <v-card>
      <v-card-title>
        <span class="text-h5">Добавить точку</span>
      </v-card-title>
      <v-card-text>
        <v-text-field
          v-model="pointName"
          :error-messages="nameErrors"
          label="Название точки"
          required
          @input="$v.pointName.$touch()"
          @blur="$v.pointName.$touch()"
        ></v-text-field>
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
  },

  data: () => ({
    pointName: "",
  }),

  computed: {
    nameErrors() {
      const errors = [];
      if (!this.$v.pointName.$dirty) return errors;
      !this.$v.pointName.required &&
        errors.push('Поле "Название точки" не должно быть пустым!');
      return errors;
    },
  },

  methods: {
    save() {
      this.$v.$touch();
      if (!this.$v.$invalid) {
        this.createPoint({ pointName: this.pointName });
        this.$v.$reset();
        this.closeDialog();
      }
    },
    close() {
      this.$v.$reset();
      this.pointName = "";
      this.closeDialog();
    },
  },
};
</script>