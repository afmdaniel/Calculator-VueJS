<template>
  <div class="calculator">
    <Display :value="displayValue" />
    <Button label="AC" @click="clearMemory" />
    <Button label="±" @click="addDigit" />
    <Button label="%" @click="setOperation" />
    <Button label="÷" operation @click="setOperation" />
    <Button label="7" @click="addDigit" />
    <Button label="8" @click="addDigit" />
    <Button label="9" @click="addDigit" />
    <Button label="x" operation @click="setOperation" />
    <Button label="4" @click="addDigit" />
    <Button label="5" @click="addDigit" />
    <Button label="6" @click="addDigit" />
    <Button label="-" operation @click="setOperation" />
    <Button label="1" @click="addDigit" />
    <Button label="2" @click="addDigit" />
    <Button label="3" @click="addDigit" />
    <Button label="+" operation @click="setOperation" />
    <Button label="0" double @click="addDigit" />
    <Button label="." @click="addDigit" />
    <Button label="=" operation @click="setOperation" />
  </div>
</template>

<script>
import Display from "../components/Display";
import Button from "../components/Button";

const Calculator = {
  components: {
    Button,
    Display
  },
  data: function() {
    return {
      displayValue: "0",
      clearDisplay: false,
      operation: null,
      values: [0, 0],
      currentIndex: 0
    };
  },
  methods: {
    clearMemory: function() {
      Object.assign(this.$data, this.$options.data());
    },
    setOperation: function(operation) {
      let operationResolved = false;
      const values = [...this.values];
      
      if (this.currentIndex !== 0) {
        const currentOperation = this.operation;

        switch (currentOperation) {
          case "%":
            values[0] *= values[1] / 100;
            break;
          case "÷":
            values[0] /= values[1];
            values[0] = parseFloat(values[0].toFixed(3));
            break;
          case "x":
            values[0] *= values[1];
            break;
          case "+":
            values[0] += values[1];
            break;
          case "-":
            values[0] -= values[1];
            break;
          default:
        }

        operationResolved = operation === "=";
        values[1] = 0;
      }

      this.displayValue = values[0].toString();
      this.clearDisplay = true;
      this.operation = operationResolved ? null : operation;
      this.values = values;
      this.currentIndex = operationResolved ? 0 : 1;
    },
    addDigit(n) {
      if (n === "." && this.displayValue.includes(".")) {
        return;
      }

      let currentValue;
      let displayValue;

      if (n === "±") {
        currentValue = this.displayValue;
        displayValue =
          currentValue.indexOf("-") === -1
            ? "-" + currentValue
            : currentValue.replace("-", "");
      } else {
        const regex = /-?0/;
        const clearDisplay = regex.test(this.displayValue) || this.clearDisplay;

        if (this.displayValue.indexOf("-") !== -1 && !this.clearDisplay) {
          currentValue = clearDisplay ? "-" : this.displayValue;
        } else {
          currentValue = clearDisplay ? "" : this.displayValue;
        }
        
        displayValue = currentValue + n;
      }

      if (n !== ".") {
        const newValue = parseFloat(displayValue);
        this.values[this.currentIndex] = newValue;
      }

      this.displayValue = displayValue;
      this.clearDisplay = false;
    }
  }
};

export default Calculator;
</script>

<style>
.calculator {
  height: 320px;
  width: 235px;
  border-radius: 5px;
  overflow: hidden;

  display: grid;
  grid-template-columns: repeat(4, 25%);
  grid-template-rows: 1fr 48px 48px 48px 48px 48px;
}
</style>