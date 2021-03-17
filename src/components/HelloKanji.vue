<template>
  <div class="container main">
    <!-- TODO: customize kanji rank (e.g., only id > 1000) -->
    <!-- TODO: button ROLL new kanji -->
    <section class="hero is-small is-link block">
      <div class="hero-body">
        <h1 class="title kanji-title pretty-font">{{ kanji.kanji }}</h1>
      </div>
    </section>

    <!-- Readings and links -->
    <div class="columns is-centered">
      <div class="column">
        <div class="tags are-small is-right">
          <span
            v-for="m in kanji.meanings"
            :key="m"
            class="tag is-light is-warning is-lowercase"
            >{{ m }}</span
          >
        </div>
      </div>
      <div class="column">
        <div class="tags are-large is-centered">
          <span v-if="kanji.on" class="tag is-light is-info">{{
            kanji.on
          }}</span>
          <span v-if="kanji.kun" class="tag is-light is-primary">{{
            kanji.kun
          }}</span>
        </div>
      </div>
      <div class="column">
        <!-- Links to other resources -->
        <div class="tags are-small is-left">
          <span class="tag is-light is-link">
            <a target="_blank" :href="'https://yourei.jp/' + kanji.kanji"
              >examples</a
            >
          </span>
          <span class="tag is-light is-link">
            <a target="_blank" :href="'https://kotobank.jp/word/' + kanji.kanji"
              >thesaurus</a
            >
          </span>
        </div>
      </div>
    </div>

    <!-- Additional data -->
    <div v-if="kanji.jp_meanings" class="columns is-centered block">
      <div class="column">
        <div class="tags are-medium is-centered">
          <span
            v-for="m in kanji.jp_meanings"
            :key="m"
            class="tag is-light is-error is-small"
            >{{ m }}</span
          >
        </div>
      </div>
    </div>

    <!-- Similar kanji -->
    <div class="columns is-centered box">
      <div class="buttons is-centered">
        <button
          v-for="k in kanji.group"
          :key="k"
          href="#"
          @click="setKanji(k)"
          class="button is-large is-outlined is-link pretty-font"
          :class="{ 'is-focused': k === kanji.kanji }"
        >
          {{ k }}
        </button>
      </div>
    </div>

    <br />

    <!-- Examples -->
    <!-- TODO: make KANJI ONLY clickable -->
    <div class="columns is-centered block">
      <table class="table examples has-text-left">
        <tr v-for="w in kanji.words" :key="w">
          <!-- Highlight kanji in word -->
          <td class="example-kanji is-size-4" v-html="highlight(w.kanji)"></td>
          <td class="example-reading">{{ w.reading }}</td>
          <td class="example-meaning is-size-7">{{ w.meaning }}</td>
        </tr>
      </table>
    </div>
  </div>
</template>

<script>
const API = "";
export default {
  name: "HelloKanji",
  props: {},
  async mounted() {
    this.fetchRandomKanji();
    this.fetchLocalKanjiData();
  },
  data() {
    return {
      // kanjiData: kanjiDB
      kanjiData: null,
      // Current kanji (dummy)
      kanji: {
        kanji: "龠",
        on: "ヤク",
        kun: "ふえ",
        group: ["龠", "龡", "龢", "龣", "龤", "龥"],
        meanings: ["flute"],
      },
    };
  },
  methods: {
    // Set kanji by key
    setKanji(kanji) {
      this.kanji = this.kanjiData[kanji];
    },
    // Get random kanji key
    getRandomKanji() {
      let availableKanji = Object.keys(this.kanjiData);
      return availableKanji[Math.floor(Math.random() * availableKanji.length)];
    },
    // Update current kanji
    setRandomKanji() {
      this.kanji = this.kanjiData[this.getRandomKanji()];
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
    /////////////////////////////////
    /* Async (potentially) methods */
    /////////////////////////////////
    // Load local assets on demand
    async fetchLocalKanjiData() {
      const data = await fetch("./kanji.json");
      this.kanjiData = await data.json();
    },
    // Fetch kanji from remote json storage
    async fetchKanji(kanji_id) {
      const result = await fetch(
        `https://paraio.com/v1/kanji/${kanji_id}?accessKey=app:kanji-odysseus`,
      )
      this.kanji = await result.json()
    },
    // Fetch random kanji
    fetchRandomKanji() {
      this.fetchKanji(Math.floor(Math.random() * 2001));
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
