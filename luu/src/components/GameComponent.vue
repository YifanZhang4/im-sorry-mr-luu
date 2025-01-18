<template>
  <div class="sans">
    <div v-if="health === 0">
      <div role="alert" class="alert alert-error">
        <svg
          xmlns="http://www.w3.org/2000/svg"
          class="h-6 w-6 shrink-0 stroke-current"
          fill="none"
          viewBox="0 0 24 24"
        >
          <path
            stroke-linecap="round"
            stroke-linejoin="round"
            stroke-width="2"
            d="M10 14l2-2m0 0l2-2m-2 2l-2-2m2 2l2 2m7-2a9 9 0 11-18 0 9 9 0 0118 0z"
          />
        </svg>
        <span>Wow! You Lost! Congrats! Please Imagine Me Clapping!</span>
      </div>
    </div>
    <div class="flex justify-center">
      <img
        src="../../public/icons/1.png"
        class="max-w-[40vw]"
        v-if="health === 7"
      />
      <img
        src="../../public/icons/2.png"
        class="max-w-[40vw]"
        v-if="health === 6"
      />
      <img
        src="../../public/icons/3.png"
        class="max-w-[40vw]"
        v-if="health === 5"
      />
      <img
        src="../../public/icons/4.png"
        class="max-w-[40vw]"
        v-if="health === 4"
      />
      <img
        src="../../public/icons/5.png"
        class="max-w-[40vw]"
        v-if="health === 3"
      />
      <img
        src="../../public/icons/6.png"
        class="max-w-[40vw]"
        v-if="health === 2"
      />
      <img
        src="../../public/icons/7.png"
        class="max-w-[40vw]"
        v-if="health === 1"
      />
    </div>
    <div>
      <form
        @submit.prevent="handleSubmit"
        class="flex flex-row justify-center mt-7"
      >
        <input
          v-model="input"
          type="text"
          placeholder="Type here"
          class="input input-bordered w-full max-w-xs mr-6"
        />
        <button class="btn" @click="submitGuess">Submit</button>
      </form>
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
    <div class="flex justify-center">
      <button class="btn" @click="funny">Funny Button</button>
    </div>
  </div>
</template>

<script setup>
import { onMounted, ref } from "vue";
import freewrite from "./freewrite.js";
import copypasta from "./copypasta.js";

const input = ref();
const current = ref("");
const index = ref(0);
const alreadyGuessed = ref(false);
const wrong = ref(false);
const guessed = ref([]);
const words = ref(Array.from(freewrite.split(" ")));
const letters = ref([]);
const emptySpaces = ref([]);
const emptyWords = ref([]);
const health = ref(7);
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
  "’",
];

const funny = () => {
  console.log("hey");
  words.value = Array.from(copypasta.split(" "));
  emptyWords.value = [];
  current.value = "";
  letters.value = [];
  emptySpaces.value = [];
  index.value = 0;
  guessed.value = [];
  health.value = 7;
  getWords();
  getCurrent();
  getLetters();
};

const getWords = () => {
  for (let i = 0; i < words.value.length; i++) {
    emptyWords.value.push("________");
  }
};

const getCurrent = () => {
  current.value = words.value[index.value];
};

const getLetters = () => {
  for (let i = 0; i < current.value.length; i++) {
    if (punctuationArray.includes(current.value[i])) {
      letters.value.push(current.value[i]);
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
  } else {
    wrong.value = false;
  }
  guessed.value.push(guess);

  input.value = "";

  if (emptySpaces.value.every((space) => space !== "_")) {
    emptyWords.value[index.value] = words.value[index.value];
    index.value++;
    letters.value = [];
    emptySpaces.value = [];
    guessed.value = [];
    health.value = 7;
    getCurrent();
    getLetters();
  }

  if (health.value === 0) {
    const reload = () => {
      window.location = window.location;
    };
    setInterval(reload, 3000);
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
  getWords();
  getCurrent();
  getLetters();
});
</script>

<style scoped>
.sans {
  font-family: "Comic Sans MS", "Comic Sans", cursive;
}
</style>
