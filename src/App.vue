<script setup>
// This starter template is using Vue 3 <script setup> SFCs
// Check out https://vuejs.org/api/sfc-script-setup.html#script-setup
import { onMounted, reactive, computed } from "vue";
import SimpleKeyboard from "./components/SimpleKeyboard.vue";
import WordRow from "./components/WordRow.vue";

const state = reactive({
  solution: "books",
  guesses: ["", "", "", "", "", ""],
  currentGuessIndex: 0,
  guessedLetter: {
    miss: [],
    found: [],
    hint: [],
  },
});

const wonGame = computed(
  () => state.guesses[state.currentGuessIndex - 1] === state.solution
);
const lostGame = computed(() => !wonGame.value && state.currentGuessIndex >= 6);

const handleInput = (key) => {
  const currentGuess = state.guesses[state.currentGuessIndex];

  if (state.currentGuessIndex >= 6 || wonGame.value) {
    return;
  }

  if (key == "{enter}") {
    if (currentGuess.length == 5) {
      state.currentGuessIndex++;
      setTimeout(() => {
        for (var i = 0; i < 5; i++) {
          let c = currentGuess.charAt(i);
          if (c == state.solution.charAt(i)) {
            state.guessedLetter.found.push(c);
          } else if (state.solution.indexOf(c) != -1) {
            state.guessedLetter.hint.push(c);
          } else {
            state.guessedLetter.miss.push(c);
          }
        }
      }, 2500);
    }
  } else if (key == "{bksp}") {
    state.guesses[state.currentGuessIndex] = currentGuess.slice(0, -1);
  } else if (currentGuess.length < 5) {
    const alphaRegex = /[a-z]/;
    if (alphaRegex.test(key)) {
      state.guesses[state.currentGuessIndex] += key;
    }
  }
};

onMounted(() => {
  window.addEventListener("keyup", (e) => {
    e.preventDefault();
    let key =
      e.keyCode == 13
        ? "{enter}"
        : e.keyCode == 8
        ? "{bksp}"
        : String.fromCharCode(e.keyCode).toLowerCase();
    handleInput(key);
  });
});
</script>

<template>
  <div class="flex flex-col justify-evenly h-screen max-w-md mx-auto">
    <WordRow
      v-for="(guess, i) in state.guesses"
      :key="i"
      :value="guess"
      :submitted="i < state.currentGuessIndex"
      :solution="state.solution"
    />
    <p v-if="wonGame" class="text-center">🏆 Congrats you solved it!</p>
    <p v-else-if="lostGame" class="text-center">😔 Out of tries.</p>
    <simple-keyboard @onKeyPress="handleInput" :guessedLetter="state.guessedLetter" />
  </div>
</template>

<style></style>
