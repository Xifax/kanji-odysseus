<template>
  <div class="container main">
    <!-- TODO: button ROLL new kanji -->
    <section
      class="hero is-small is-link block clickable"
      @click="setRandomKanji()"
    >
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
    <!-- TODO: split meaning by japanese dot if the length is too wide -->
    <div v-if="kanji.jp_meanings" class="columns is-centered block">
      <div class="column">
        <div class="tags are-medium is-centered">
          <span
            v-for="m in splitLongLines(kanji.jp_meanings)"
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
    <!-- TODO: make ONLY KANJI clickable -->
    <div class="columns is-centered is-full-width block">
      <div class="column is-half">
        <table class="table has-text-left is-fullwidth">
          <tr v-for="w in kanji.words" :key="w">
            <!-- Highlight kanji in word -->
            <td
              class="example-kanji is-size-4"
              v-html="highlight(w.kanji)"
            ></td>
            <td class="example-reading">{{ w.reading }}</td>
            <td class="example-meaning is-size-7">{{ w.meaning }}</td>
          </tr>
        </table>
      </div>
    </div>
  </div>

  <br />

  <!-- Footer with settings and links -->
  <footer class="footer">
    <div class="content">
      <div class="columns">
        <div class="column is-one-quarter">
          <p class="has-text-left has-text-grey-light pretty-font">
            Kanji Odysseus
          </p>
        </div>
        <div class="column">
          <div class="buttons is-right">
            <button
              class="button is-small is-outlined is-danger"
              @click="showModal = true"
            >
              <span class="icon is-small">
                <i class="fas fa-tools"></i>
              </span>
            </button>
            <!-- TODO: link to github -->
            <a href="https://github.com/Xifax" class="button is-small">
              <span class="icon is-small">
                <i class="fab fa-github"></i>
              </span>
            </a>
          </div>
        </div>
      </div>
    </div>
  </footer>

  <!-- Modal settings -->
  <div class="modal" :class="{ 'is-active': showModal }">
    <div class="modal-background"></div>
    <div class="modal-content">
      <div class="container box">
        <section class="hero is-small block">
          <div class="hero-body">
            <p class="title pretty-font">Tweaks</p>
          </div>
        </section>
        <div class="field has-addons">
          <p class="control">
            <a class="button is-static">漢字 min frequency</a>
          </p>
          <div class="control">
            <input
              v-model="minFreq"
              class="input"
              type="number"
              min="0"
              placeholder="0"
            />
          </div>
        </div>
        <div class="field has-addons">
          <p class="control">
            <a class="button is-static">漢字 max frequency</a>
          </p>
          <div class="control">
            <input
              v-model="maxFreq"
              class="input"
              type="number"
              min="0"
              placeholder="0"
            />
          </div>
        </div>
        <div class="field has-text-left">
          <label class="checkbox">
            <input v-model="saveKanji" type="checkbox" />
            If possible, don't repeat the same kanji twice
            <em class="has-text-grey-light">(will save already shown)</em>
          </label>
        </div>

        <div class="field is-grouped">
          <div class="control">
            <button class="button is-link" @click="saveSettings()">Save</button>
          </div>
        </div>
      </div>
    </div>
    <button
      class="modal-close is-large"
      @click="saveSettings()"
      aria-label="close"
    ></button>
  </div>
</template>

<script>
const API = "";
export default {
  name: "HelloKanji",
  props: {},
  async mounted() {
    let kanjiData = localStorage.getItem("kanjiData");
    // Either get kanji data from local storage
    if (kanjiData !== null) {
      this.kanjiData = JSON.parse(kanjiData);
      this.setRandomKanji();
      // Or fetch kanji from API and initialize local storage
    } else {
      this.fetchRandomKanji();
      this.fetchLocalKanjiData();
    }

    // Load settings
    this.minFreq = localStorage.getItem("minFreq") || 0;
    this.maxFreq = localStorage.getItem("maxFreq") || 9999;
    this.saveKanji = localStorage.getItem("saveKanji") || false;
    let savedKanji = localStorage.getItem("savedKanji");
    if (savedKanji != null) {
      this.savedKanji = JSON.parse(savedKanji);
    } else {
      localStorage.setItem("savedKanji", JSON.stringify([]));
    }
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
      showModal: false,
      savedKanji: [],
      minFreq: 0,
      maxFreq: 9999,
      saveKanji: false,
    };
  },
  methods: {
    ///////////////
    /* UI update */
    ///////////////

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
      console.log(this.kanji.frequency);
      this.kanji = this.kanjiData[this.getRandomKanji()];
    },
    // Update local storage settings
    saveSettings() {
      localStorage.setItem("minFreq", this.minFreq);
      localStorage.setItem("maxFreq", this.maxFreq);
      localStorage.setItem("saveKanji", this.saveKanji);
      this.showModal = false;
    },
    // Update shown kanji storage
    saveKanji(kanji) {
      if(this.saveKanji) {
        if(!this.savedKanji.includes(kanji.kanji)) {
          this.savedKanji.push(kanji.kanji)
          localStorage.setItem("savedKanji", JSON.stringify(this.savedKanji))
        }
      }
    },

    ////////////////////
    /* Visual filters */
    ////////////////////

    // Split long sentences into multiple items (for non-breakable tags)
    splitLongLines(examples) {
      let results = [];
      examples.forEach((element) => {
        // Lstrip numbers
        element = element.replace(/^[0-9]+/, "");
        if (element.length > 18) {
          results.concat(element.split("。"));
        } else {
          results.push(element);
        }
      });
      return results;
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
      localStorage.setItem("kanjiData", JSON.stringify(this.kanjiData));
    },
    // Fetch kanji from remote json storage
    async fetchKanji(kanji_id) {
      const result = await fetch(
        `https://paraio.com/v1/kanji/${kanji_id}?accessKey=app:kanji-odysseus`
      );
      this.kanji = await result.json();
    },
    // Fetch random kanji
    fetchRandomKanji() {
      this.fetchKanji(Math.floor(Math.random() * 2001));
    },
  },
  computed: {
    // Separate property for random kanji from storage
    randomKanji() {
      return kanjiData[this.getRandomKanji()];
    },
  },
};
</script>
