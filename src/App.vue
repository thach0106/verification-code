
<script setup lang="ts">
import { ref, watchEffect } from "vue";


const code = ref<string[]>(Array(6));
const codeFromPaste = ref<string[]>([]);

const handleKeyPress = (event: KeyboardEvent): void => {
  isNaN(+event.key) ? event.preventDefault() : (event.currentTarget as HTMLInputElement).value = '';
}

const handleInput = (event: InputEvent): void => {
  let currentActiveElement = event.target as HTMLInputElement;

  if (event.inputType === "insertText") {
    (currentActiveElement.nextElementSibling as HTMLElement)?.focus();
  }

  if (event.inputType === "insertFromPaste" && codeFromPaste) {
    for (const number of codeFromPaste.value) {
      const currentInputId: number = parseInt(currentActiveElement.id.split("_")[1]);
      currentActiveElement.value = number;
      code[currentInputId] = number;
      if (currentActiveElement.nextElementSibling) {
        currentActiveElement =
          currentActiveElement.nextElementSibling as HTMLInputElement;
        (currentActiveElement.nextElementSibling as HTMLElement)?.focus();
      }
    }
  }
}

const handleDelete = (event: Event): void => {
  const input = event.target as HTMLInputElement;
  !input.value && (input.previousElementSibling as HTMLElement)?.focus();
}

const handlePaste = (event: ClipboardEvent): void => {
  codeFromPaste.value = event.clipboardData
    ?.getData("text")
    .trim()
    .split("");
  codeFromPaste?.some((number: string) => isNaN(+number)) && event.preventDefault();
}
</script>

<template>
  <h2>Verification Code</h2>
  <p>Please enter the code that we sent to your mobile number ***-*****.</p>
  <div class="parent">
    <div class="child">
      <form>
        <input
          v-for="(_, index) in code"
          :key="index"
          type="number"
          :id="`code_${index}`"
          pattern="\d*"
          maxlength="1"
          v-model="code[index]"
          @input="handleInput"
          @keypress="handleKeyPress"
          @keydown.delete="handleDelete"
          @paste="handlePaste"
        />
      </form>
    </div>
  </div>
</template>


<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: auto;
}
.parent {
  text-align: center;
  position: relative;
  margin: auto;
  height: 100px;
}

.child {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}

form {
  display: flex;
  flex-direction: row;
  gap: 18px;
}
input[type="number"] {
  width: 150px;
  height: 50px;
  font-size: 50px;
  text-align: center;
  caret-color: transparent !important;
}
/* Chrome, Safari, Edge, Opera */
input::-webkit-outer-spin-button,
input::-webkit-inner-spin-button {
  -webkit-appearance: none;
  margin: 0;
}

/* Firefox */
input[type="number"] {
  -moz-appearance: textfield;
}

@media only screen and (max-width: 1080px) {
  input[type="number"] {
    width: 80px;
  }
}
@media only screen and (max-width: 600px) {
  input[type="number"] {
    width: 40px;
  }
}
@media only screen and (max-width: 500px) {
  form {
    gap: 8px;
  }
  input[type="number"] {
    width: 12vw;
    font-size: 40px;
  }
}


</style>
