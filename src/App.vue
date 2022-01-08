<template>
  <img alt="Vue logo" src="./assets/logo.png" />
  <br />
  <v-autocomplete
    :data="data"
    placeholder="Search..."
    v-model="val"
  ></v-autocomplete>
</template>

<script>
import VAutocomplete from "./components/VAutocomplete.vue";

export default {
  name: "App",
  components: {
    VAutocomplete,
  },
  data() {
    return {
      data: ["Lorem", "ipsum", "sit", "dolor", "amet de"],
      val: "",
    };
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
