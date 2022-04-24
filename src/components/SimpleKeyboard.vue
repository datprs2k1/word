<template>
  <div class="simple-keyboard"></div>
</template>

<script setup>
import Keyboard from "simple-keyboard";
import "simple-keyboard/build/css/index.css";
import { ref, onMounted, watch } from "vue";

const keyboard = ref(null);

const emit = defineEmits(["onKeyPress"]);

const onKeyPress = (button) => {
  emit("onKeyPress", button);
};

const props = defineProps({
  guessedLetter: Object,
});

onMounted(() => {
  keyboard.value = new Keyboard("simple-keyboard", {
    layout: {
      default: [
        "q w e r t y u i o p",
        "a s d f g h j k l",
        "{enter} z x c v b n m {bksp}",
      ],
    },
    onKeyPress: onKeyPress,
  });
});

watch(
  () => props.guessedLetter,
  (guessedLetter, preGuessedLetter) => {
    keyboard.value.addButtonTheme(guessedLetter.miss.join(" "), "miss");
    keyboard.value.addButtonTheme(guessedLetter.found.join(" "), "found");
    keyboard.value.addButtonTheme(guessedLetter.hint.join(" "), "hint");
  },
  { deep: true }
);
</script>

<style>
div.miss {
  @apply bg-gray-500 !important;
  @apply text-white;
}
div.found {
  @apply bg-green-600 !important;
  @apply text-white;
}
div.hint:not(.found) {
  @apply bg-yellow-500 !important;
  @apply text-white;
}
</style>
