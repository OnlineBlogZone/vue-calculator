<template>
  <div id="calculator" class="p-3">

    <div class="calculator-result w-full rounded m-1 p-3 text-end lead fw-bold text-white">
      {{ calcVal || 0 }}
    </div>

    <div class="row g-0">
      <div class="calculator-buttons col-3" v-for="btn in buttons" :key="idx">
        <button class="lead text-white text-center m-1 py-3 rounded btn btn-hover"
                :class="{'bg-obz-green': ['C', '*', '/', '+', '-', '=', '%'].includes(btn)}"
                @click="buttonPress(btn)">
          {{ btn }}
        </button>
      </div>
    </div>

  </div>
</template>

<script setup>
import {ref} from 'vue';
import Decimal from 'decimal.js';

const calcVal = ref(0);
let operators = null;
let prevCalcVal = '';
const buttons = ['C', '%', '=', '+', '7', '8', '9', '-', '4', '5', '6', '*', '1', '2', '3', '/', '0', '.'];

const buttonPress = (btn) => {
  if (!isNaN(btn) || btn === '.') {
    calcVal.value += btn.toString() + '';
  } else {
    switch (btn) {
      case 'C':
        calcVal.value = '';
        break;
      case '%':
        calcVal.value = (calcVal.value / 100).toString() + '';
        break;
      case '+':
      case '-':
      case '*':
      case '/':
        operators = btn;
        prevCalcVal = calcVal.value;
        calcVal.value = '';
        break;
      case '=':
        try {
          calcVal.value = calculate(operators, prevCalcVal, calcVal.value);
        } catch (e) {
          calcVal.value = "Error";
        }
        prevCalcVal = ''
        operators = null;
        break;
      default:
        calcVal.value = "Invalid";
    }
  }
}

const calculate = (operator, prevValue, currentValue) => {
  let result = 0;
  if (prevValue === "Invalid expression" || prevValue === "Division by Zero") {
    prevValue = 0;
  }
  if (isNaN(prevValue) || prevValue === "") {
    return "Invalid expression";
  }
  prevValue = new Decimal(prevValue);
  currentValue = new Decimal(currentValue);

  switch (operator) {
    case '+':
      result = prevValue.plus(currentValue);
      break;
    case '-':
      result = prevValue.minus(currentValue);
      break;
    case '*':
      result = prevValue.times(currentValue);
      break;
    case '/':
      if (currentValue.equals(0)) {
        return "Division by Zero";
      }
      result = prevValue.dividedBy(currentValue);
      break;
    case '%':
      result = prevValue.mod(currentValue);
      break;
    default:
      return "Invalid expression";
  }

  return result.toString();
}


</script>

<style scoped>

#calculator {
  max-width: 25rem;
  margin: 3.125rem auto;
  background: var(--calculator-background-color);
}

.calculator-result {
  background: var(--main-color);
}

.calculator-buttons .btn {
  background: var(--main-color);
  color: #97989b;
  width: calc(100% - .25rem);
  margin: 2rem;
}

.calculator-buttons .bg-obz-green {
  background-color: var(--obz-green);
}
</style>