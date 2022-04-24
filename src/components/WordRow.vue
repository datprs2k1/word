<template>
  <div class="w-full grid grid-cols-5 gap-x-1 mx-auto mb-1">
    <LetterBox v-for="i in 5" :key="i" :letter="value[i - 1]" :color="color[i - 1]" />
  </div>
</template>

<script setup>
import { watch, ref } from "vue";
import LetterBox from "./LetterBox.vue";

const color = ref(["", "", "", "", ""]);

const props = defineProps({
  value: {
    type: String,
    default: "",
  },
  submitted: {
    type: Boolean,
    default: false,
  },
  solution: {
    type: String,
    default: "",
  },
});

watch(
  () => props.submitted,
  async (submitted, preSubmitted) => {
    if (props.submitted) {
      let s = props.solution;
      let v = props.value;

      let temp = ["gray", "gray", "gray", "gray", "gray"];
      let letterPool = [];

      for (let i = 0; i < 5; i++) {
        if (s.charAt(i) == v.charAt(i)) {
          temp[i] = "green";
        } else {
          letterPool.push(s.charAt(i));
        }
      }

      for (let i = 0; i < 5; i++) {
        if (temp[i] == "gray") {
          if (letterPool.indexOf(v.charAt(i)) != -1) {
            letterPool.splice(letterPool.indexOf(v.charAt(i)), 1);
            temp[i] = "yellow";
          }
        }
        color.value[i] = temp[i];
        await new Promise((resolve) => setTimeout(resolve, 500));
      }
    }
  }
);
</script>

<style lang="scss" scoped></style>
