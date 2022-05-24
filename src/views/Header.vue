<template>
  <header>
    <div class="container">
      <div class="top-bar">
        <div id="logo">{{ logoText }}</div>
        <ul class="nav-bar">
          <li v-for="navItem in navItems" :key="navItem.value">
            <a href="navItem.link">{{ navItem.value }}</a>
          </li>
        </ul>
      </div>
      <div id="title">{{ title }}</div>
      <button>{{ buttonText }}</button>
    </div>
  </header>
</template>

<script>
export default {
  name: "Header",
  data() {
    return {
      logoText: "Major Project",
      navItems: [
        { link: "#algorithms", value: "Algorithms" },
        { link: "#results", value: "Results" },
        { link: "#upload", value: "Upload" },
        { link: "#about", value: "About" },
      ],
      title:
        "Predictive Analysis for Brain Cancer Detection using Machine Learning Techniques",
      buttonText: "Try It Yourself!",
    };
  },
};
</script>

<style scoped>
header {
  height: 55vh;
  width: 100%;
  background-color: #191970;
  border-bottom-left-radius: 3rem;
  border-bottom-right-radius: 3rem;
  color: #fff;
  display: flex;
  justify-content: center;
  align-items: center;
}

.container {
  height: 90%;
  width: 80%;
}

.top-bar {
  height: 15%;
  width: 80%;
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin: 0 auto;
}

#logo {
  width: min-content;
  font-size: 1.3rem;
}

.nav-bar {
  width: 50%;
  height: 100%;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

a {
  padding: 0.5rem 1.2rem;
  border-radius: 0.5rem;
  color: #fff;
  font-size: 1.1rem;
}

a:hover {
  background-color: #fff;
  color: #000;
}

#title {
  width: 40%;
  margin: 2rem auto;
  font-size: 2rem;
  text-align: center;
}

button {
  display: block;
  border: none;
  margin: 0 auto;
  padding: 0.6rem 1.2rem;
  font-size: 1.4rem;
  background-color: rgb(238, 87, 17);
  color: #fff;
  border-radius: 0.6rem;
}

button:active {
  transform: scale(0.98);
}
</style>