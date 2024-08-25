<script setup lang="ts">
import { RouterLink, RouterView } from 'vue-router'
import Quiz from './components/Quiz.vue';
import { getCurrentInstance, onMounted, ref, watch, type Component, type Ref } from 'vue';

const componentRoot: Ref<HTMLElement | null>= ref(null);
const themeToggle = ref(false);

const lightTheme = [
  `--letter-bg: white;`,
  `--letter-hover-bg: lightgrey;`,
  `--letter-active-bg: grey;`,
  `--highlight-bg: lightgrey;`,
  `--input-border-bg: rgba(0,0,0,0.1);`,
  `--bg: rgba(0, 0, 0, 0.02);`,
  `--color-text: var(--vt-c-text-light-1);`
];

const darkTheme = [
  `--letter-bg: black;`,
  `--letter-hover-bg: grey;`,
  `--letter-active-bg: grey;`,
  `--highlight-bg: darkgrey;`,
  `--input-border-bg: rgba(255,255,255,0.1);`,
  `--bg: rgba(0, 0, 0, 0.98);`,
  `--color-text: var(--vt-c-text-dark-1);`
]

watch(themeToggle, (newValue, _) => {
  // should i be error handling here? is typecasting correct here?
  let app: HTMLElement = (componentRoot.value as HTMLElement).parentElement as HTMLElement;
  app.setAttribute("style", [lightTheme, darkTheme][+newValue].join(""));
});

</script>

<template>
  <span ref="componentRoot"></span>
  <header>
    <button id="modeButtonBtn" @click="themeToggle = !themeToggle"><div id="modeButton"> <p>🌙</p> </div></button>
    <div class="wrapper">
      <nav>
        <!-- <RouterLink to="/">Home</RouterLink>
        <RouterLink to="/about">About</RouterLink> -->  
      </nav>
    </div>
  </header>
  <!-- <RouterView /> -->
  <Quiz></Quiz>
</template>

<style>
:root {
  --letter-bg: white;
  --letter-hover-bg: lightgrey;
  --letter-active-bg: grey;
  --highlight-bg: lightgrey;
  --input-border-bg: rgba(0, 0, 0, 0.1);
  --bg: rgba(0, 0, 0, 0.02)
}

#app {
  background: var(--bg);
}

#modeButtonBtn {
  all: unset;
  display: inline-block;
  cursor: pointer;
}

#modeButton {
  margin: 5px;
  padding: 0;
  background-color: none;
  font-size: calc(var(--font-size)/5);
}
</style>