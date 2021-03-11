<template>
  <h2>{{ msg }}</h2>
  <h1>{{ kanji.kanji }}</h1>
  <!-- TODO: apply cool styles for everything -->
  <a v-for="k in kanji.group"
    :key="k"
    href="#"
    @click="setKanji(k)">{{ k }}&nbsp;</a>
    <!-- TODO: get meaning from odyssey? -->
  <p>{{ kanji.on }}</p>
  <p>{{ kanji.kun }}</p>
  <!-- TODO: make responsive? -->
  <div v-for="w in kanji.words" 
    :key="w">
    <!-- Highlight kanji in word -->
    <div v-html="highlight(w.kanji)"></div>
    {{ w.reading }}
    {{ w.meaning }}
    </div>
</template>

<script>
import kanjiData from '../assets/kanji.json'
export default {
  name: 'HelloKanji',
  props: {
    msg: String
  },
  mounted() {
    this.setRandomKanji()
  },
  data() {
    return {
      // Current kanji
      kanji: ''
    }
  },
  methods: {
    // Set kanji by key
    setKanji(kanji) {
      this.kanji = kanjiData[kanji]
    },
    // Get random kanji key
    getRandomKanji() {
      let availableKanji = Object.keys(kanjiData)
      return availableKanji[Math.floor(Math.random() * availableKanji.length)]
    },
    // Update current kanji
    setRandomKanji() {
      this.kanji = kanjiData[this.getRandomKanji()]
    },
    // Replace current kanji with some custom formatting
    highlight(word) {
      return word.replace(this.kanji.kanji, `<strong>${this.kanji.kanji}</strong>`)
    },
  },
  computed: {
    // Separate property for current kanji
    randomKanji() {
      return kanjiData[this.getRandomKanji()]
    }
  }
}
</script>
