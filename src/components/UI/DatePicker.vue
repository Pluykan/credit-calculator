<template>
  <v-col cols="12" md="4">
    <v-menu
      v-model="menu2"
      :close-on-content-click="false"
      :nudge-right="40"
      transition="scale-transition"
      offset-y
      min-width="auto"
    >
      <template v-slot:activator="{ on, attrs }">
        <v-text-field
          v-model="date"
          label="Дата"
          readonly
          v-bind="attrs"
          v-on="on"
          color="#3E885B"
        ></v-text-field>
      </template>
      <v-date-picker
        color="#2F4B26"
        v-model="date"
        @input="selectDate"
      ></v-date-picker>
    </v-menu>
  </v-col>
</template>

<script>
export default {
  data: () => ({
    date: new Date(Date.now() - new Date().getTimezoneOffset() * 60000)
      .toISOString()
      .substr(0, 10),
    menu: false,
    modal: false,
    menu2: false,
  }),
  methods: {
    selectDate() {
      this.menu2 = false;
      this.$emit("data", this.date);
    },
  },
};
</script>

<style lang="scss" scoped></style>
