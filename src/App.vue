<template>
  <div>
    <Header />
    <Algorithms />
    <Results />
    <Upload />
    <Footer />
  </div>
</template>

<script>
import Header from "./views/Header.vue";
import Algorithms from "./views/Algorithms.vue";
import Results from "./views/Results.vue";
import Upload from "./views/Upload.vue";
import Footer from "./views/Footer.vue";

export default {
  name: "App",
  components: {
    Header,
    Algorithms,
    Results,
    Upload,
    Footer,
  },
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Poppins:wght@100;300;400;500;600;700&display=swap");

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: "Poppins", sans-serif;
}

ul {
  list-style-type: none;
}

a {
  text-decoration: none;
}

button {
  border: none;
  cursor: pointer;
}
</style>
