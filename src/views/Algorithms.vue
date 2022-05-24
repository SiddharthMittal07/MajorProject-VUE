<template>
  <section class="outer-algo">
    <div id="algorithms">
      <div v-if="data.length" class="container">
        <div class="title-description-matrix">
          <div class="title-description">
            <div id="title">{{ data[index].algorithm }}</div>
            <div id="desc">{{ data[index].information.main }}</div>
          </div>
        </div>
        <div class="features">
          <div class="col">
            <span>Applications: </span>
            <ul>
              <li
                v-for="item in data[index].information.applications"
                :key="item"
              >
                &gt; {{ item }}
              </li>
            </ul>
          </div>
          <div class="col">
            <span>Pros: </span>
            <ul>
              <li v-for="item in data[index].information.pros" :key="item">
                &gt; {{ item }}
              </li>
            </ul>
          </div>
          <div class="col">
            <span>Cons: </span>
            <ul>
              <li v-for="item in data[index].information.cons" :key="item">
                &gt; {{ item }}
              </li>
            </ul>
          </div>
        </div>
      </div>
    </div>
    <div class="nav-bar">
      <button @click="decrementIndex" :disabled="index === 0">&lt;</button>
      <button @click="incrementIndex" :disabled="index === data.length - 1">
        &gt;
      </button>
    </div>
  </section>
</template>

<script>
const url =
  "https://gist.githubusercontent.com/SiddharthMittal07/554b26ec04129f393e2fc59c82c0f1d3/raw/871224e25d4eb30b19eb7c2a945f7db0af405787/major_project_define.json";

export default {
  name: "Algorithms",
  data() {
    return {
      index: 0,
      data: [],
    };
  },
  methods: {
    incrementIndex() {
      if (this.index < this.data.length - 1) {
        this.index += 1;
      }
    },
    decrementIndex() {
      if (this.index > 0) {
        this.index -= 1;
      }
    },
  },
  async mounted() {
    const response = await fetch(url);
    this.data = await response.json();
  },
};
</script>

<style scoped>
.outer-algo {
  height: 55vh;
  width: 100%;
  position: relative;
  margin-bottom: 1rem;
}

#algorithms {
  position: absolute;
  top: -4rem;
  left: 50%;
  transform: translateX(-50%);
  height: 110%;
  width: 65%;
  background-color: #fff;
  border: 1px solid rgba(0, 0, 0, 0.2);
  border-radius: 2rem;
  display: flex;
  justify-content: center;
  align-items: center;
}

.container {
  height: 90%;
  width: 95%;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}

.title-description-matrix {
  height: 40%;
  width: 100%;
  display: flex;
}

.title-description {
  height: 100%;
  width: 60%;
}

#title {
  font-size: 2rem;
  color: #191970;
  margin-bottom: 0.5rem;
}

#description {
  font-size: 1.1rem;
}

.features {
  height: 50%;
  width: 100%;
  display: flex;
  justify-content: space-around;
}

.col {
  height: 100%;
  width: 32%;
  padding: 0.5rem;
  border-radius: 1rem;
  background-color: rgba(200, 200, 200, 0.2);
  cursor: pointer;
}

.col:hover {
  background-color: rgba(0, 0, 0, 0.1);
}

.nav-bar {
  margin: 0 auto;
  height: 80%;
  width: 80%;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

button {
  font-size: 1.7rem;
  border: none;
  padding: 0.5rem 1rem;
  border-radius: 0.5rem;
  font-weight: bold;
  background-color: #191970;
  color: #fff;
}

button:active {
  transform: scale(0.98);
}

button:disabled {
  color: #555;
  background-color: #ded;
  cursor: not-allowed;
  transform: scale(1);
}
</style>