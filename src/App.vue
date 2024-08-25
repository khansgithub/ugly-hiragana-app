<script setup lang="ts">
import { RouterLink, RouterView } from 'vue-router'
import Quiz from './components/Quiz.vue';
import { ref, watch } from 'vue';

const themeToggle = ref(false);

const lightTheme = [
  `--letter-bg: white;`,
  `--letter-hover-bg: lightgrey;`,
  `--letter-active-bg: grey;`,
  `--highlight-bg: lightgrey;`,
  `--input-border-bg: black;`,
  `--bg: rgba(0, 0, 0, 0.02)`
];

const darkTheme = [
  `--letter-bg: black;`,
  `--letter-hover-bg: darkgrey;`,
  `--letter-active-bg: cream;`,
  `--highlight-bg: darkgrey;`,
  `--input-border-bg: white;`,
  `--bg: rgba(0, 0, 0, 0.98)`
]

const theme = ref<string[]>();

watch(themeToggle, (newValue, oldValue) => {
  theme.value = themeToggle ? darkTheme : lightTheme;
});

</script>

<template>
  <header>
    <div id="modeButton"><button @click="themeToggle = !themeToggle"> dark </button></div>
    <div class="wrapper">
      <nav>
        <!-- <RouterLink to="/">Home</RouterLink>
        <RouterLink to="/about">About</RouterLink> -->
      </nav>
    </div>
  </header>
  <!-- <RouterView /> -->
  <main :style="theme?.join('')">
    <Quiz></Quiz>
  </main>
</template>

<style>
:root {
  --letter-bg: white;
  --letter-hover-bg: lightgrey;
  --letter-active-bg: grey;
  --highlight-bg: lightgrey;
  --input-border-bg: black;
  --bg: rgba(0, 0, 0, 0.02)
}

body {
  background: var(--bg);
}

main {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

#modeButton {
  margin: 0;
  padding: 0;
  background-color: none;
}
</style>