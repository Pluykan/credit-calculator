<template>
  <div class="credit_creator">
    <div class="info_block">
      <span><strong>Дата: </strong> {{ credit.date }} </span>
      <span><strong>Сумма: </strong> {{ credit.sum }}</span>
      <span><strong>Срок: </strong> {{ credit.term }} </span>
      <span><strong>Процент: </strong> {{ credit.percent }} </span>
      <span><strong>Тип калькулятора: </strong> {{ credit.type }} </span>
    </div>
    <div class="inputs_block">
      <v-form>
        <v-container>
          <v-row>
            <DatePicker @data="onDate"></DatePicker>
            <Input label="Сумма" :field="credit.sum" @get="onSum"></Input>
            <Input label="Срок" :field="credit.term" @get="onTerm"></Input>
            <Input label="Процент" :field="credit.percent" @get="onPercent"></Input>
            <Select label="Тип калькулятора" color="#2f4b26" :items="types" @getItem="onType"></Select>
          </v-row>
        </v-container>
      </v-form>
    </div>
    <v-btn depressed @click="$emit('get', credit)" x-large text color="#2f4b26"> Посчитать </v-btn>
  </div>
</template>

<script>
import DatePicker from "./UI/DatePicker.vue";
import Select from "./UI/Select.vue";
import Input from "./UI/Input.vue";
export default {
  name: "CreditCreator",
  components: { DatePicker, Select, Input },
  data: () => ({
    credit: {
      date: new Date(Date.now() - new Date().getTimezoneOffset() * 60000)
        .toISOString()
        .substr(0, 10),
      sum: "0",
      term: "0",
      percent: "0",
      type: "",
    },
    types: ['Дифференциальный', 'Аннуитетный'],
  }),
  methods: {
    onDate(data) {
      this.credit.date = data;
    },
    onType(data) {
      this.credit.type = data;
    },
    onSum(data) {
      this.credit.sum = data;
    },
    onTerm(data) {
      this.credit.term = data;
    },
    onPercent(data) {
      this.credit.percent = data;;
    },
  },
};
</script>
<style scoped>
.credit_creator {
  background-color: #85bda6;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  color: hsl(105, 33%, 22%);
  padding: 20px;
  flex: 1;
}

.info_block {
  display: flex;
  flex-direction: column;
  width: 35vw;
  border: 2px solid #2f4b26;
  border-radius: 20px;
  padding: 20px;
}

.inputs_block {
  display: flex;
  padding: 20px;
}
</style>
