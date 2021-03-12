<template>
  <div class="container">
    <!-- TODO: customize kanji rank (e.g., only id > 1000) -->
    <!-- TODO: button ROLL new kanji -->
    <section class="hero is-small is-link box">
      <div class="hero-body">
        <h1 class="title kanji-title">{{ kanji.kanji }}</h1>
      </div>
    </section>
    <!-- TODO: apply cool styles for everything -->
    <div class="columns is-centered box">
        <div class="field is-grouped">
          <p
            class="control"
            v-for="k in kanji.group"
            :key="k"
            href="#"
            @click="setKanji(k)"
          >
            <button class="button is-medium is-outlined is-link">
              {{ k }}
            </button>
          </p>
        </div>
    </div>

    <!-- TODO: get meaning from odyssey? -->
    <!-- TODO: check some readings: TAMA, ATAI, ― -->
    <div class="container block">
      <span class="tag is-light is-info is-large">{{ kanji.on }}</span>
      <span class="tag is-light is-primary is-large">{{ kanji.kun }}</span>
    </div>

    <!-- Highlight kanji in word -->
    <!-- TODO: make KANJI ONLY clickable -->
    <div class="columns is-centered">
      <table class="table">
        <tr v-for="w in kanji.words" :key="w">
          <td v-html="highlight(w.kanji)"></td>
          <td>{{ w.reading }}</td>
          <td>{{ w.meaning }}</td>
        </tr>
      </table>

      <!-- <div v-for="w in kanji.words" :key="w">
        <span v-html="highlight(w.kanji)"></span>
        {{ w.reading }}
        {{ w.meaning }}
      </div> -->
    </div>
  </div>
</template>

<script>
import kanjiData from "../assets/kanji.json";
export default {
  name: "HelloKanji",
  props: {},
  mounted() {
    this.setRandomKanji();
  },
  data() {
    return {
      // Current kanji
      kanji: "",
    };
  },
  methods: {
    // Set kanji by key
    setKanji(kanji) {
      this.kanji = kanjiData[kanji];
    },
    // Get random kanji key
    getRandomKanji() {
      let availableKanji = Object.keys(kanjiData);
      return availableKanji[Math.floor(Math.random() * availableKanji.length)];
    },
    // Update current kanji
    setRandomKanji() {
      this.kanji = kanjiData[this.getRandomKanji()];
    },
    // Replace current kanji with some custom formatting
    highlight(word) {
      return word.replace(
        this.kanji.kanji,
        `<strong>${this.kanji.kanji}</strong>`
      );
    },
    // Make kanji clickable
    referenceKanji(word) {
      // TODO: scan each character and wrap it with @click="setKanji(?)" ???
    },
    // Check if character is kanji
    isKanji(char) {
      return /[\u4e00-\u9faf\u3400-\u4dbf]/.test(char);
    },
  },
  computed: {
    // Separate property for current kanji
    randomKanji() {
      return kanjiData[this.getRandomKanji()];
    },
  },
};
</script>
