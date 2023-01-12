<template>
  <v-card>
    <v-card-title>
      <span class="font-weight-bold text-h5">Информация о точке</span>
    </v-card-title>
    <v-card-text>
      <v-card-subtitle class="card-sub-title card-title mt-6">
        <span>ID:</span>
      </v-card-subtitle>
      <v-card-title class="card-sub-title card-subtitle">
        <span>
          {{ selectedPoint.id }}
        </span>
      </v-card-title>
      <v-card-subtitle class="card-sub-title card-title mt-5">
        <span>Название:</span>
      </v-card-subtitle>
      <v-card-title class="card-sub-title card-subtitle">
        <span>
          {{ selectedPoint.pointName }}
        </span>
      </v-card-title>
      <v-card-subtitle class="card-sub-title card-title mt-5">
        <span>Автор:</span>
      </v-card-subtitle>
      <v-card-title class="card-sub-title card-subtitle">
        <span>
          {{ selectedPoint.authorName }}
        </span>
      </v-card-title>
      <v-card-subtitle class="card-sub-title card-title mt-5">
        <span>Время создания:</span>
      </v-card-subtitle>
      <v-card-title class="card-sub-title card-subtitle">
        <span>
          {{ formattedDateTime }}
        </span>
      </v-card-title>
      <v-card-subtitle class="card-sub-title card-title mt-5">
        <span>Координаты:</span>
      </v-card-subtitle>
      <v-card-title class="card-sub-title card-subtitle">
        <span>
          {{ selectedPoint.latlng }}
        </span>
      </v-card-title>
    </v-card-text>
    <v-card-actions>
      <v-spacer></v-spacer>
      <v-btn
        color="red darken-1"
        text
        @click="() => deletePoint(selectedPoint.id)"
      >
        Удалить
      </v-btn>
      <v-btn color="blue darken-1" text @click="closeDialog"> Закрыть </v-btn>
    </v-card-actions>
  </v-card>
</template>

<script>
export default {
  props: {
    closeDialog: {
      type: Function,
    },
    selectedPoint: {
      type: Object,
    },
    deletePoint: {
      type: Function,
    },
  },
  computed: {
    formattedDateTime() {
      const addZero = (value) => String(value).padStart(2, "0");
      const date = new Date(this.selectedPoint.createdAt);

      const dateFormat = date.toLocaleDateString("ru-RU", {
        year: "numeric",
        month: "long",
        day: "numeric",
      });
      const timeFormat = `${addZero(date.getHours())}:${addZero(
        date.getMinutes()
      )}:${addZero(date.getSeconds())}`;

      return `${dateFormat} ${timeFormat}`;
    },
  },
};
</script>

<style lang="scss">
.card-sub-title {
  padding: 0;
}

.card-subtitle {
  color: white;
}
</style>