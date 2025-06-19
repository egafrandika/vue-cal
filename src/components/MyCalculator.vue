<template>
  <div class="calculator">
    <div class="display">{{ display }}</div>
    <div class="buttons">
      <button v-for="btn in buttons" :key="btn.label"
        :class="[btn.class || '', btn.label === '=' ? 'equals' : '', btn.label === '0' ? 'zero' : '']"
        @click="handleClick(btn)">
        {{ btn.label }}
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const display = ref('0');

const buttons = [
  { label: '+', class: 'operator' },
  { label: '-', class: 'operator' },
  { label: '*', class: 'operator' },
  { label: '/', class: 'operator' },
  { label: '7' },
  { label: '8' },
  { label: '9' },
  { label: '=', class: 'equals' },
  { label: '4' },
  { label: '5' },
  { label: '6' },
  { label: '1' },
  { label: '2' },
  { label: '3' },
  { label: '0', class: 'zero' },
  { label: '.', class: '' },
  { label: 'AC' },
];

const append = (char) => {
  const operators = ['+', '-', '*', '/'];
  if (display.value === '0') {
    if (char === '.') {
      display.value = '0.';
    } else if (operators.includes(char)) {
      // Ignore operator if display is at initial state
      return;
    } else {
      display.value = char;
    }
  } else {
    if (operators.includes(char) && operators.includes(display.value.slice(-1))) {
      return;
    }
    // Prevent multiple dots in the current number
    if (char === '.') {
      // Find the last operator in the display
      const lastOperatorIndex = Math.max(
        display.value.lastIndexOf('+'),
        display.value.lastIndexOf('-'),
        display.value.lastIndexOf('*'),
        display.value.lastIndexOf('/')
      );
      const currentNumber = display.value.slice(lastOperatorIndex + 1);
      if (currentNumber.includes('.')) {
        return;
      }
    }
    display.value += char;
  }
};

const clear = () => {
  display.value = '0';
};

const calculate = () => {
  try {
    let result = eval(display.value.replace(/÷/g, '/').replace(/×/g, '*'));
    display.value = String(result);
  } catch {
    display.value = 'Error';
  }
};

const handleClick = (btn) => {
  if (btn.label === 'AC') {
    clear();
  } else if (btn.label === '=') {
    calculate();
  } else {
    append(btn.label);
  }
};
</script>
<style scoped>
.calculator {
  width: 320px;
  margin: 40px auto;
  border-radius: 20px;
  box-shadow: 0 2px 20px #bbb;
  background: #f9f9f9;
  padding: 0;
  overflow: hidden;
}

.display {
  background: #181818;
  color: #fff;
  font-size: 2.5em;
  text-align: right;
  padding: 30px 20px 20px 10px;
  border-radius: 0 0 0 0;
  margin-bottom: 0;
  min-height: 60px;
  letter-spacing: 1px;
}

.buttons {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  grid-template-rows: repeat(5, 60px);
  gap: 0;
}

button {
  font-size: 1.3em;
  border: 1px solid #e0e0e0;
  background: #fff;
  cursor: pointer;
  transition: background 0.2s;
  outline: none;
  border-radius: 0;
}

button:active {
  background: #e0e0e0;
}

button.operator {
  background: #f5f5f5;
  color: #222;
  font-weight: bold;
}

button.equals {
  background: #e07a53;
  color: #fff;
  grid-row: 2 / span 4;
  grid-column: 4 / 5;
  font-size: 1.5em;
  border-radius: 0 0 20px 0;
}

button.zero {
  grid-column: 1;
}
</style>
