<template>
  <v-col cols="12" md="4">
    <v-text-field
      color="#3E885B"
      v-model="localField"
      :label="label"
      required
    ></v-text-field>
  </v-col>
</template>

<script>
export default {
  props: {
    field: {
      type: String,
      required: true,
    },
    label: {
      type: String,
      required: true,
    },
  },
  computed: {
    localField: {
      get() {
        return this.field;
      },
      set(value) {
        this.$emit("get", value);
      },
    }
  },
  
};
</script>

<style lang="scss" scoped></style>
