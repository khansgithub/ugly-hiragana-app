<script setup lang="ts">
import { RouterLink, RouterView } from 'vue-router'
import Quiz from './components/Quiz.vue';
import { getCurrentInstance, onMounted, ref, watch, type Component } from 'vue';

const componentRoot = ref<HTMLElement | null>(null);
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

watch(themeToggle, (newValue, oldValue) => {
  console.log(themeToggle);
  if (!componentRoot.value) return;
  let app: HTMLElement = componentRoot.value as HTMLElement;
  if (!app.parentElement) {return};
  app = app.parentElement;

  let theme:string[] = newValue ? darkTheme : lightTheme;
  app.setAttribute("style", theme.join(""));
});

onMounted(() => {
  
})

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
  --input-border-bg: rgba(0,0,0,0.1);
  --bg: rgba(0, 0, 0, 0.02)
}

#app {
  background: var(--bg);
}

#modeButtonBtn{
  all: unset; /* Remove all default styles */
  display: inline-block; /* Maintain button-like behavior */
  cursor: pointer; /* Ensure the cursor shows as a pointer */
}

#modeButton {
  margin: 5px;
  padding: 0;
  background-color: none;
  font-size: calc(var(--font-size)/5);
}
</style>