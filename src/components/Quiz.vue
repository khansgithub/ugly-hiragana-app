<script setup lang="ts">
  import { words, kb_data } from '@/data/words.ts';
  import { ref, reactive, Ref, computed, onMounted, nextTick, watch, type TextareaHTMLAttributes } from 'vue'
  
  interface Words {
    english: string
    romanji: string
    hiragana: string
  };

  const word: Ref<Words> = ref({ english: "", romanji: "", hiragana: "" });
  const inputBoxText: Ref<string> = ref("");

  const letterRomanjiHoverValue: Ref<string> = ref("");
  const letterRomanjiHoverElem = ref(null);
  const letterRomanjiClasses = ref({
    letterRomanjiHover: true,
    letter: true,
    fadeIn: false,
    fadeOut: false,
    animationPause: false
  });

  const opacityValue: Ref<number> = ref(0.0);
  const blinkClass: Ref<string> = ref("");
  
  function updateWordValue(): Words {
    let w: Words =  words[randomNumber(0, words.length - 1)];
    Array.from(document.getElementsByClassName("highlight")).forEach(function (highlighted_letter) {
      highlighted_letter.classList.remove("highlight");
    });

    for (let letter of w.hiragana){
      try{
        let l = document.getElementById("h-char-"+letter)
        document.getElementById("h-char-"+letter)?.classList.add("highlight");
      } catch (err) {
        console.error(letter);
        console.log(err);
      }
    }
    return w;
  }

  function updateWord(){
    word.value = updateWordValue();
  }

  function randomNumber(bottom: number, top: number): number {
    return Math.floor(Math.random() * (1 + top - bottom)) + bottom;
  }

  function letterHover(romanji: string) {
    return `--romanji-hover: "${romanji}"`;
  }

  function submitWord(){
    let correct_answer = validateWord();
    updateBlinkClass(correct_answer);
    if (correct_answer){
      updateWord();
      inputBoxText.value = '';
    }
  }

  function validateWord() {
    return inputBoxText.value === word.value.hiragana;
  }

  // are side effects like this good...? 
  function updateBlinkClass(correctAnswer: boolean) {
    blinkClass.value = correctAnswer ? "blink-right" : "blink-wrong";
  }

  function letterRomanjiHoverEnter() {
    letterRomanjiClasses.value.animationPause = true;

    letterRomanjiClasses.value.fadeOut = false

    opacityValue.value = getCurrentOpacity();
    letterRomanjiClasses.value.fadeIn = false;
    letterRomanjiClasses.value.fadeIn = true;
    
    letterRomanjiClasses.value.animationPause = false;
  }

  function letterRomanjiHoverLeave() {
    letterRomanjiClasses.value.animationPause = true;
    
    letterRomanjiClasses.value.fadeIn = false;
    opacityValue.value = getCurrentOpacity();

    letterRomanjiClasses.value.fadeOut = false;
    letterRomanjiClasses.value.fadeOut = true;
    
    letterRomanjiClasses.value.animationPause = false;
  }
  function getCurrentOpacity(){
    let elem = letterRomanjiHoverElem.value as unknown as HTMLElement;
    let elem_styles = window.getComputedStyle(elem);
    return parseFloat(elem_styles.opacity)
  }
  
  onMounted(() => {
    updateWord();
  });

</script>

<template>
  <h1 id="word" v-text="word.english" :class="blinkClass" @animationend="blinkClass = ''"></h1>
  <h4 id="word_eng" v-text="word.romanji"></h4>
  <button id="next_word" @click="e => submitWord()"> > </button>
  <textarea class="input-box" v-text="inputBoxText"></textarea>
  <div class="kb">  
    <div ref="letterRomanjiHoverElem" :class="letterRomanjiClasses"><p>{{ letterRomanjiHoverValue }}</p></div>
    <div class="row" v-for="row in kb_data">
      <div class="letter"
        :id="`h-char-${hiragana}`"
        :romanji="`${romanji}`"
        :style="letterHover(romanji)"
        @mouseenter="e => {
          letterRomanjiHoverEnter();
          letterRomanjiHoverValue = romanji;
        }"
        @mouseleave="e => letterRomanjiHoverLeave()"
        v-for="(romanji, hiragana) in row"
      >
        <p @click="e => inputBoxText += (e.target as HTMLDivElement).parentElement?.id.split('-')[2] ?? ''">{{ hiragana
          }}</p>
      </div>
    </div>
  </div>
</template>

<style>
:root {
  --letter-width: auto;
  /*    --letter-width: 25px;*/
  --romanji-hover: '';
  --font-size: 2.5em;
  --font-family: monospace;
  --blink-correct: green;
  --blink-incorrect: red;
}

@keyframes blink {
  100% {
    color: var(--blink-colour, unset);
  }
}

.blink-wrong {
  --blink-colour: var(--blink-incorrect);
  animation: blink 1s alternate-reverse;
}

.blink-right {
  --blink-colour: var(--blink-correct);
  animation: blink 1s alternate-reverse;
}

#app {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  font-size: var(--font-size);
  font-family: var(--font-family);
}

.kb {
  display: flex;
  flex-direction: column;
  position: relative;
}

.row {
  display: flex;
  flex-direction: row;
  justify-content: center;
}

.row p {
  margin: 0;
  padding: 0;
}

.letter {
  /*    position: relative;*/
  background: var(--letter-bg);
  width: var(--letter-width);
  height: var(--letter-width);
  border: 1px solid var(--input-border-bg);
  text-align: center;
  transition: background ease-in-out 0.2s;
  cursor: default;
  user-select: none;
}

.letter:hover {
  background: var(--letter-hover-bg);
}

.letter:active {
  transition: background-color ease-in-out 0.0s;
  background: (--letter-active-bg);
}

.letter p {
  display: contents;
}

.indent0-5 {
  margin-left: calc(var(--letter-width)/2);
}

.indent1 {
  margin-left: calc(var(--letter-width));
}

.indent1-5 {
  margin-left: calc(var(--letter-width)*1.5);
}

.indent2 {
  margin-left: calc(var(--letter-width)*2);
}

#word {
  margin-bottom: 0;
}

.highlight {
  background-color: var(--highlight-bg);
}

textarea.input-box{
  border: 1px dashed var(--input-border-bg);
  width: 30%;
  height: var(--font-size);
  font-size: calc(var(--font-size)/2);
  text-align: center;
  background: var(--bg);
  color: var(--color-text);
  margin-bottom: 2px;
}

.letterRomanjiHover {
  /* height: 100px;
  width: 100px;
  background:red; */
  height: 0;
  width: 0;
  border: none;
  display: flex;
  justify-content: center;
  /* align-items: center; */
  position: absolute;
  right: 0;
  top: 40%;
  line-height: 3px;
  /* opacity: 0; */
}

.fadeIn {
  animation: fade-in 0.5s;
  animation-fill-mode: forwards;
}

.fadeOut {
  animation: fade-out 0.5s;
  animation-fill-mode: forwards;
}

.animationPause{
  animation-play-state: paused;
}

@keyframes fade-in {
  from {
    opacity: v-bind("opacityValue")
  }

  to {
    opacity:1;
  }
}

@keyframes fade-out {
  from {
    opacity: v-bind("opacityValue");
  }

  to {
    opacity: 0;
  }
}

#word_eng{
  color: grey;
  margin: 0;
  padding: 0;
}
</style>