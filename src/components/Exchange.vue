<template>
  <div class="wrapper">
    <h1>Конвертор валют</h1>
    <div class="container">
      <section class="row">
        <input type="number" v-model="amount_one" @input="calculateResults" />
      </section>
      <section class="row centered">
        <select v-model="currency_one" @change="calculateResults">
          <option v-for="country in countries" :key="country" :value="country">
            {{ country }}
          </option>
        </select>

        <div class="icon">
          <i class="fas fa-exchange-alt" @click="switchCurrency"></i>
        </div>

        <select v-model="currency_two" @change="calculateResults">
          <option v-for="country in countries" :key="country" :value="country">
            {{ country }}
          </option>
        </select>
      </section>
      <section class="row">
        <input type="number" v-model="amount_two" @input="calculateResults" />
      </section>
      <p class="rate">{{ baseRate }}</p>
      <div class="row">
        <p class="rate" id="lastlyUpdated">
          Останнє оновлення: {{ lastlyUpdated }}
        </p>
      </div>
    </div>
  </div>
</template>

<script>
import { ref } from "vue";
import axios from "axios";
import countries from "./countries.js";
export default {
  setup() {
    const currency_one = ref(countries.USD);
    const amount_one = ref(1);
    const currency_two = ref(countries.UAH);
    const amount_two = ref(0);
    const baseRate = ref("");
    const lastlyUpdated = ref("");
    const calculateResults = async () => {
      if (amount_one.value <= 0) {
        amount_two.value = 0;
        return;
      }
      const api_key = "91a90aac71149b647200c818";
      const url = `https://v6.exchangerate-api.com/v6/${api_key}/latest/${currency_one.value}`;

      try {
        const response = await axios.get(url);
        const rate = response.data.conversion_rates[currency_two.value];
        amount_two.value = (amount_one.value * rate).toFixed(2);
        lastlyUpdated.value = response.data.time_last_update_utc;
        baseRate.value = `1 ${currency_one.value} = ${rate} ${currency_two.value}`;
      } catch (error) {
        console.error("NO respose from API", error);
      }
    };

    const switchCurrency = () => {
      if (amount_one.value <= 0) {
        amount_one.value = 1;
      }
      const temp = currency_one.value;
      currency_one.value = currency_two.value;
      currency_two.value = temp;
      calculateResults();
    };

    calculateResults();
    return {
      calculateResults,
      switchCurrency,
      countries,
      currency_one,
      amount_one,
      currency_two,
      amount_two,
      lastlyUpdated,
      baseRate,
    };
  },
};
</script>

<style lang="scss" scoped>
.wrapper {
  max-width: 320px;
  padding: 30px;
  margin: 0 15px;
  border-radius: 7px;
  background: #fff;
  box-shadow: 7px 7px 20px rgba(0, 0, 0, 0.5);

  & h1 {
    font-size: 28px;
    text-align: center;
    margin-bottom: 20px;
  }
}

.container {
  display: flex;
  flex-direction: column;

  .row:not(:last-child) {
    display: flex;
    margin-bottom: 20px;
    &.centered {
      align-items: center;
      justify-content: center;
    }
    select {
      padding: 5px;
      margin: 0px 20px;
    }
    input {
      border: 1px solid #999;
      border-radius: 7px;
      outline: none;
      width: 100%;
      margin: 0 auto;
      height: 50px;
      font-size: 18px;
      padding: 0px 15px;
    }
  }

  .icon {
    cursor: pointer;
  }

  .rate {
    text-align: center;
    line-height: 1.5;
    &:not(:last-child) {
      margin-bottom: 20px;
    }
  }

  #lastlyUpdated {
    font-size: 16px;
  }
}

@media (max-width: 480px) {
  .wrapper {
    max-width: 300px;

    & h1 {
      font-size: 24px;
    }
  }

  .row input {
    width: 260px;
    height: 50px;
    font-size: 18px;
    padding: 0px 15px;
  }
}
</style>
