<template>
  <div>
    <v-data-table
      
      :headers="headers"
      :items="monthes"
      item-key="monthNumber"
      class="elevation-1"
    >
      <template v-slot:item.actions="{ item }">
        <v-btn depressed @click="deleteItem(item)" x-small text color="#375a6b">
          Закрыть
        </v-btn>
      </template>
    </v-data-table>
  </div>
</template>

<script>
export default {
  name: "CreditMonthesList",
  props: {
    monthes: {
      type: Array,
      required: true,
    },
  },
  methods: {
    deleteItem(item) {
      this.editedIndex = this.monthes.indexOf(item);
      this.$emit('delete', item.MD);
      this.editedItem = Object.assign({}, item);
      this.monthes.splice(this.editedIndex, 1);
      this.editedIndex = -1;

    },
  },
  data: () => ({
    editedMonth: {
      monthNumber: "",
      date: "",
      DP: "",
      MD: "",
      P: "",
      OD: "",
    },
    headers: [
      {
        text: "№",
        align: "start",
        sortable: false,
        value: "monthNumber",
      },
      { text: "Дата", value: "date" },
      { text: "Платёж", value: "DP" },
      { text: "Осн. долг", value: "MD" },
      { text: "Процент", value: "P" },
      { text: "Остаток", value: "OD" },
      { text: "Actions", value: "actions", sortable: false },
    ],
  }),
};
</script>

<style lang="scss" scoped>
.v-data-table {
  background-color: hsl(212, 97%, 87%);
  border: 2px solid #375a6b;
  color: #375a6b;
}
</style>
