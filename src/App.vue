<template>
  <!-- <img alt="Vue logo" src="./assets/logo.png" /> -->
  <HelloKanji />
</template>

<script>
import HelloKanji from "./components/HelloKanji.vue";
// TODO: include https://docs.netlify.com/site-deploys/overview/#deploy-contexts
// TODO: include jsconfig.json https://vuejs.github.io/vetur/guide/FAQ.html#how-to-build-and-install-from-source
// TODO: configure prettier and so on

export default {
  name: "App",
  components: {
    HelloKanji,
  },
};
</script>

<style lang="sass">
@import "./bulma/bulma.sass"
@import url('https://fonts.googleapis.com/css2?family=Shippori+Mincho+B1:wght@400&display=swap')
@import "theme.scss"
</style>