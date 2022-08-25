<template>
  <div class="body">
    <CreditCreator @get="getCredit"></CreditCreator>
    <CreditCalculation ref="childRef" :credit="credit"></CreditCalculation>
  </div>
</template>

<script>
import CreditCreator from "@/components/CreditCreator.vue";
import CreditCalculation from "../components/CreditCalculation.vue";
export default {
  name: "CalculatorView",
  components: {
    CreditCreator,
    CreditCalculation,
  },
  data: () => ({
    credit: {
      date: null,
      sum: "0",
      term: "0",
      percent: "0",
      type: "",
    },
  }),
  methods: {
    getCredit(data) {
      this.credit = { ...data };
      if (this.credit.type === "Дифференциальный") {
        this.$refs.childRef.getDifCalculation(this.credit);
      } else if (this.credit.type === "Аннуитетный") {
        this.$refs.childRef.getAnnCalculation(this.credit);
      }
    },
  },
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

html {
  overflow: hidden;
}

.body {
  display: flex;
  flex-direction: row;
  justify-content: center;
  height: 100vh;
  overflow: hidden;
}
</style>
