<template>
  <div class="credit_calculation">
    <div class="credit_amount">
      Сумма кредита: {{ Number(credit.sum).toFixed(2) }}
    </div>
    <div class="repayment">
      <Select
        @getItem="getRepaymentType"
        color="#375a6b"
        label="Тип погашения"
        :items="repaymentTypes"
      ></Select>
      <Input
        label="Сумма погашения"
        :field="repayment.sum.toString()"
        @get="onRepayment"
      ></Input>
      <v-btn depressed @click="changeSum" large text color="#2f4b26">
        Погасить
      </v-btn>
    </div>
    <CreditMonthesList
      @delete="deleteSumOfMonth"
      :monthes="monthes"
    ></CreditMonthesList>
  </div>
</template>

<script>
import CreditMonthesList from "./CreditMonthesList.vue";
import Input from "./UI/Input.vue";
import Select from "./UI/Select.vue";
export default {
  name: "CreditCalcution",
  props: {
    credit: {
      type: Object,
    },
  },
  data: () => ({
    monthes: [],
    repayment: {
      type: "",
      sum: "",
    },
    repaymentTypes: ["Обычное", "Досрочное"],
  }),
  methods: {
    onRepayment(data) {
      this.repayment.sum = data;
    },
    getRepaymentType(data) {
      this.repayment.type = data;
    },
    changeSum() {
      if (Number(this.repayment.sum) >= Number(this.credit.sum)) {
        this.credit.sum = "0";
        this.monthes = [];
        this.repayment.sum = "";
      } else {
        this.credit.sum -= Number(this.repayment.sum);
        if (this.credit.type === "Дифференциальный") {
          if (this.repayment.type === "Досрочное") {
            this.getDifRepayment();
          } else if (this.repayment.type === "Обычное") {
            this.getSimpleRepayment();
          }
        } else if (this.credit.type === "Аннуитетный") {
          if (this.repayment.type === "Досрочное") {
            this.getAnnCalculation(this.credit);
          } else if (this.repayment.type === "Обычное") {
            this.getSimpleRepayment();
          }
        }
      }
    },
    deleteSumOfMonth(MD) {
      this.credit.sum -= Number(MD);
    },
    daysInMonth(moneth, year) {
      return new Date(year, month, 0).getDate();
    },
    getAnnCalculation(credit) {
      this.monthes = [];
      let OD = Number(credit.sum);
      let i = Number(credit.percent) / 100 / 12; //годовая процентная ставка (0.2 для 20%)
      let K =
        (i * Math.pow(1 + i, Number(credit.term))) /
        (Math.pow(1 + i, Number(credit.term)) - 1); //коэффициент аннуитета
      let A = Number(credit.sum) * Number(K); //аннуитетный платеж
      for (let j = 0; j < credit.term; j++) {
        let dateOfMonth;
        if (j >= 1) {
          dateOfMonth = new Date(this.monthes[j - 1].date);
          dateOfMonth.setMonth(dateOfMonth.getMonth() + 1);
        } else {
          dateOfMonth = new Date(credit.date);
          dateOfMonth.setMonth(dateOfMonth.getMonth() + 1);
        }
        let p = (Math.pow(1.1, this.daysInMonth(dateOfMonth.getMonth(), dateOfMonth.getFullYear()) / 365) - 1) * Number(OD);
        let MD = Number(A) - Number(p);
        OD = Number(OD) - Number(MD);

        let newMonth = {
          monthNumber: j + 1,
          date: dateOfMonth.toISOString().substr(0, 10),
          DP: A.toFixed(2),
          MD: MD.toFixed(2),
          P: p.toFixed(2),
          OD: OD.toFixed(2),
        };
        this.monthes.push(newMonth);
      }
    },
    getDifCalculation(credit) {
      this.monthes = [];
      let DP; //дифференцированный платеж
      let OD = Number(credit.sum);
      for (let j = 0; j < credit.term; j++) {
        let MD = Number(credit.sum) / Number(credit.term); //основной долг
        let P =
          (Number(OD) * Number(credit.percent)) / 100 / Number(credit.term); //процент
        DP = Number(MD) + Number(P); // дифференцированный платеж
        OD = Number(OD) - Number(MD); // остаток
        let dateOfMonth;
        if (j >= 1) {
          dateOfMonth = new Date(this.monthes[j - 1].date);
          dateOfMonth.setMonth(dateOfMonth.getMonth() + 1);
        } else {
          dateOfMonth = new Date(credit.date);
          dateOfMonth.setMonth(dateOfMonth.getMonth() + 1);
        }
        let newMonth = {
          monthNumber: j + 1,
          date: dateOfMonth.toISOString().substr(0, 10),
          MD: MD.toFixed(2),
          P: P.toFixed(2),
          DP: DP.toFixed(2),
          OD: OD.toFixed(2),
        };
        this.monthes.push(newMonth);
      }
    },
    getDifRepayment() {
      this.monthes.forEach((month, index) => {
        if (Number(this.repayment.sum) >= Number(month.MD)) {
          this.repayment.sum -= Number(month.MD);
          month.OD = Number(this.credit.sum);
          this.monthes.forEach((aMonth, aIndex) => {
            aMonth.P = (
              (Number(aMonth.OD) * Number(this.credit.percent)) /
              100 /
              12
            ).toFixed(2);
            if (aIndex > 0 && Number(aMonth.MD) != 0) {
              aMonth.OD = (
                Number(this.monthes[aIndex - 1].OD) - Number(aMonth.MD)
              ).toFixed(2);
            }
          });
          month.OD = Number(this.credit.sum);
          month.DP = Number(month.P).toFixed(2);
          month.MD = 0;
        } else {
          month.MD = (Number(month.MD) - Number(this.repayment.sum)).toFixed(2);
          month.DP = (Number(month.DP) - Number(this.repayment.sum)).toFixed(2);
          month.OD = (
            Number(this.monthes[index - 1].OD) - Number(month.MD)
          ).toFixed(2);
          this.repayment.sum = "";
        }
      });
    },
    getSimpleRepayment() {
      const newMonthArray = [];
      [...this.monthes].forEach((month, index) => {
        if (this.repayment.sum >= Number(month.DP)) {
          this.repayment.sum -= Number(month.DP);
        } else {
          month.DP -= this.repayment.sum;
          if (this.repayment.sum >= Number(month.P)) {
            this.repayment.sum -= Number(month.P);
            month.P = "0";
            month.MD -= this.repayment.sum.toString();
            month.MD = Number(month.MD).toFixed(2);
          } else {
            month.P -= Number(this.repayment.sum);
            month.P = Number(month.P).toFixed(2);
          }
          this.repayment.sum = 0;
          newMonthArray.push(month);
        }
      }, (this.monthes = newMonthArray));
    },
  },
  components: { CreditMonthesList, Input, Select },
};
</script>

<style scoped>
.credit_calculation {
  background-color: hsl(212, 97%, 87%);
  display: flex;
  flex-direction: column;
  align-items: center;
  color: #375a6b;
  padding: 20px;
  flex: 1;
  overflow-y: scroll;
}
.credit_amount {
  border: #375a6b 2px solid;
  border-radius: 20px;
  padding: 20px;
  margin-top: 20px;
  margin-bottom: 10px;
}
.repayment {
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: center;
  width: 45vw;
}
</style>
