<script setup lang="ts">
  import '../assets/quiz.css';
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
  const letterRomanjiHoverElem:Ref<HTMLElement|null> = ref(null);
  const letterRomanjiClasses: Ref<any> = ref({
    letterRomanjiHover: true,
    letter: true,
    fadeIn: false,
    fadeOut: false,
    animationPause: false
  });

  // always fade FROM the current opacity value to 0 or 1
  const opacityValue: Ref<number> = ref(0);
  const blinkClass: Ref<string> = ref("");
  
  function updateWordValue(): Words {
    // not sure what's a more efficient method for this
    // maybe create a whole list of Refs for each ke?
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

  function submitWord(){
    let correct_answer = validateWord();
    updateBlinkClass(correct_answer);
    if (correct_answer){
      updateWord();
      inputBoxText.value = '';
    }
  }

  function validateWord(): boolean {
    return inputBoxText.value === word.value.hiragana;
  }

  // are side effects like this good...? 
  function updateBlinkClass(asnwerIsCorrect: boolean) {
    blinkClass.value = ["blink-wrong", "blink-right"][+asnwerIsCorrect]
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

  function getCurrentOpacity(): number{
    let elem = letterRomanjiHoverElem.value as HTMLElement;
    let elem_styles = window.getComputedStyle(elem);
    return parseFloat(elem_styles.opacity);
  }
  
  function inputBoxTextUpdate(e: Event | InputEvent){
    // let key: string | null = e.data;
    // inputBoxText.value += e.data;
    // console.log(e);
  }

  onMounted(() => {
    updateWord();
  });

</script>

<template>
  <h1 id="word" v-text="word.english" :class="blinkClass" @animationend="blinkClass = ''"></h1>
  <h4 id="word-eng" v-text="word.romanji"></h4>
  <div>
    <button id="next-word" @click="submitWord()"> > </button><button id="clear-input" @click="inputBoxText = ''"> X </button>
  </div>
  <textarea class="input-box" v-text="inputBoxText" @beforeinput.prevent="e => inputBoxTextUpdate(e)"></textarea>
  <div class="kb">  
    <div ref="letterRomanjiHoverElem" :class="letterRomanjiClasses"><p>{{ letterRomanjiHoverValue }}</p></div>
    <div class="row" v-for="row in kb_data">
      <div class="letter"
        :id="`h-char-${hiragana}`"
        @mouseenter="letterRomanjiHoverEnter(); letterRomanjiHoverValue = romanji;"
        @mouseleave="letterRomanjiHoverLeave()"
        v-for="(romanji, hiragana) in row"
      >
        <p @click="e => inputBoxText += (e.target as HTMLDivElement).parentElement?.id.split('-')[2] ?? ''">{{ hiragana }}</p>
      </div>
    </div>
  </div>
</template>

<style>
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
</style>