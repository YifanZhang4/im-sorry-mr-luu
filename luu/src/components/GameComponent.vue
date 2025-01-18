<template>
  <div>
    <div class="flex justify-center">
      <img :src="getMan()" alt="" class="max-w-[40vw]" />
    </div>
    <div class="flex flex-row justify-center mt-7">
      <input
        v-model="input"
        type="text"
        placeholder="Type here"
        class="input input-bordered w-full max-w-xs mr-6"
      />
      <button class="btn" @click="submitGuess">Submit</button>
    </div>
    <div>
      <p v-if="alreadyGuessed" class="text-center">
        You've already guessed that letter!
      </p>
      <p v-if="wrong" class="text-center">That letter is wrong!</p>
    </div>
    <div class="flex flex-row justify-center mt-3">
      <div class="flex flex-row justify-center mt-3">
        <p v-for="(letter, index) in emptySpaces" :key="index" class="mr-2">
          {{ letter === "_" ? "_" : letter }}
        </p>
      </div>

      <!-- <p v-for="emptySpace in emptySpaces" :key="emptySpace" class="mr-2">
        {{ emptySpace }}
      </p> -->
    </div>
    <div class="flex flex-row justify-start flex-wrap m-3">
      <p v-for="emptyWord in emptyWords" :key="emptyWord" class="mr-2">
        {{ emptyWord }}
      </p>
    </div>
  </div>
</template>

<script setup>
import { onMounted, ref } from "vue";
import freewrite from "./freewrite.js";

const input = ref();
const current = ref("");
const index = ref(0);
const alreadyGuessed = ref(false);
const wrong = ref(false);
const guessed = ref([]);
const words = Array.from(freewrite.split(" "));
const letters = ref([]);
const emptySpaces = ref([]);
const emptyWords = ref([]);
const health = ref(7);
const image = ref(1);
const punctuationArray = [
  ".",
  ",",
  "!",
  "?",
  ";",
  ":",
  "-",
  "(",
  ")",
  "[",
  "]",
  "{",
  "}",
  '"',
  "'",
  "`",
];

const getMan = () => {
  const picture = image.value.toString();
  return "/public/icons/" + picture + ".png";
};

const getWords = () => {
  for (let i = 0; i < words.length; i++) {
    emptyWords.value.push("________");
  }
};

const getCurrent = () => {
  current.value = words[index.value];
};

const getLetters = () => {
  for (let i = 0; i < current.value.length; i++) {
    if (punctuationArray.includes(current.value[i])) {
      emptySpaces.value.push(current.value[i]);
    } else {
      letters.value.push(current.value[i]);
      emptySpaces.value.push("_");
    }
  }
};

const submitGuess = () => {
  const guess = input.value.toLowerCase();
  if (guessed.value.includes(guess)) {
    alreadyGuessed.value = true;
  }
  let found = false;
  for (let i = 0; i < letters.value.length; i++) {
    if (guess === letters.value[i].toLowerCase()) {
      emptySpaces.value[i] = letters.value[i];
      guessed.value.push(input.value.toLowerCase());
      found = true;
    }
  }

  if (!found) {
    wrong.value = true;
    health.value--;
    image.value++;
    getMan();
  } else {
    wrong.value = false;
  }
  guessed.value.push(guess);

  input.value = "";

  if (emptySpaces.value.every((space) => space !== "_")) {
    emptyWords.value[index.value] = words[index.value];
    index.value++;
    letters.value = [];
    emptySpaces.value = [];
    guessed.value = [];
    health.value = 7;
    image.value = 1;
    getCurrent();
    getLetters();
  }
};

if (alreadyGuessed) {
  const setFalse = () => {
    alreadyGuessed.value = false;
  };
  setInterval(setFalse, 3000);
}

if (wrong) {
  const setFalse = () => {
    wrong.value = false;
  };
  setInterval(setFalse, 3000);
}

onMounted(() => {
  getMan();
  getWords();
  getCurrent();
  getLetters();
});
</script>

<style scoped></style>
