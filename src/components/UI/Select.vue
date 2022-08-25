<template>
  <v-col class="d-flex" cols="12" md="4">
    <v-select
      :color="color"
      :items="items"
      v-model="currentItem"
      @input="$emit('getItem', currentItem)"
      :label="label"
    ></v-select>
  </v-col>
</template>

<script>
export default {
  props: {
    items: {
      type: Array,
      required: true,
    },
    label: {
      type: String,
    },
    color: {
      type: String,
    },
  },
  data: () => ({
    currentItem: "",
  }),
};
</script>

<style scoped>
.v-select {
  cursor: pointer;
}
</style>
