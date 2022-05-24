<template>
  <section id="upload">
    <div class="title-description">
      <div id="title">{{ title }}</div>
      <div id="description">{{ description }}</div>
    </div>
    <div id="display">
      <div id="form" v-if="firstTime">
        <label for="uploadImage">Upload Image</label>
        <form @submit.prevent="setImageAction">
          <input type="file" name="upImage" @change="uploadPicture" />
          <input type="submit" value="Upload" />
        </form>
      </div>
      <div id="img-result" v-if="!firstTime">
        <div class="image-and-form">
          <img id="image" :src="picturePreview" alt="MRI" />
          <div id="form">
            <label for="image-input">Upload MRI Image</label>
            <form @submit.prevent="setImageAction">
              <input type="file" name="upImage" @change="uploadPicture" />
              <input type="submit" value="Upload" />
            </form>
          </div>
        </div>
        <div class="upload-results" v-if="results.length">
          <svg :width="width" :height="height">
            <g
              :style="{
                transform:
                  'translate(' + margin.left + 'px, ' + margin.top + 'px)',
              }"
            >
              <g
                :style="{
                  transform:
                    'translate(' +
                    innerWidth / 2 +
                    'px, ' +
                    innerHeight / 2 +
                    'px)',
                }"
              >
                <path
                  v-for="(d, i) in arcs"
                  :key="i"
                  :d="ar(d)"
                  :fill="colorScale(d.data[0])"
                />
              </g>
              <g
                :style="{
                  transform:
                    'translate(' +
                    (innerWidth - margin.right / 3) +
                    'px, ' +
                    margin.top * 4 +
                    'px)',
                }"
              >
                <g v-for="(d, i) in colorScale.domain()" :key="i">
                  <rect
                    width="20"
                    height="10"
                    :y="i * 20"
                    :fill="colorScale(d)"
                  />
                  <text :y="i * 20" dy="0.71em" x="25" fill="#000">
                    {{ d }}
                  </text>
                </g>
              </g>
              <g
                :style="{
                  transform:
                    'translate(' +
                    innerWidth / 2 +
                    'px, ' +
                    innerHeight +
                    'px)',
                }"
              >
                <text style="{'font-weight', 'bold'}">
                  Prediction: {{ predictedTumor }}
                </text>
              </g>
            </g>
          </svg>
        </div>
      </div>
    </div>
  </section>
</template>

<script>
import { arc, pie, select, scaleOrdinal } from "d3";

export default {
  name: "Upload",
  data() {
    return {
      title: "Upload",
      description:
        "Test our Supervised Machine Learning Algorithms by uploading an MRI Image of a brain and check the results. The server returns the results of all 6 algorithms and they are displayed as a Donut Chart. The final prediction depends on the result of all algorithms.",
      firstTime: true,
      picturePreview: null,
      results: [],
      apiUrl: "http://localhost:5000/prediction",
      width: window.innerWidth * 0.3125,
      height: window.innerHeight * 0.426,
      predictedTumor: "",
      hoverLegend: null,
    };
  },
  computed: {
    innerWidth() {
      return this.width - this.margin.left - this.margin.right;
    },
    innerHeight() {
      return this.height - this.margin.top - this.margin.bottom;
    },
    innerRadius() {
      return this.width / 4;
    },
    margin() {
      return {
        top: this.width / 60,
        left: 0,
        right: this.width / 4,
        bottom: this.width / 60,
      };
    },
    outerRadius() {
      return this.width / 6;
    },
    ar() {
      return arc().innerRadius(this.innerRadius).outerRadius(this.outerRadius);
    },
    hoveredArc() {
      return arc()
        .innerRadius(this.innerRadius + 10)
        .outerRadius(this.outerRadius - 10);
    },
    pi() {
      return pie()
        .padAngle(0.04)
        .value((d) => d[1]);
    },
  },
  methods: {
    uploadPicture(e) {
      this.results = [];

      this.picturePreview = URL.createObjectURL(e.target.files[0]);
      this.pictureAsFile = e.target.files[0];
    },
    async setImageAction(e) {
      this.firstTime = false;
      const formData = new FormData();
      formData.append("upImage", this.pictureAsFile);
      console.log("Starting...");
      const res = await fetch(this.apiUrl, {
        method: "post",
        header: {
          "Content-Type": "multipart/form-data",
        },
        body: formData,
      });
      console.log("Finished..");
      const response = await res.json();
      this.results = response;

      this.colorScale = scaleOrdinal()
        .domain(this.results.map((r) => r.result))
        .range(["red", "blue", "green", "yellow"]);
      console.log(response);

      let data = {};
      this.results.forEach((d) => {
        if (!data.hasOwnProperty(d.result)) {
          data[d.result] = 0;
        }
        data[d.result] += 1;
      });
      data = Object.entries(data);
      this.arcs = this.pi(data);

      let predictions = {};
      this.results.forEach((res) => {
        if (!predictions.hasOwnProperty(res.result)) {
          predictions[res.result] = 0;
        }
        predictions[res.result] += 1;
      });

      let pred = [];
      Object.keys(predictions).forEach((key) =>
        pred.push([key, predictions[key]])
      );
      pred.sort(function (a, b) {
        return b[1] - a[1];
      });
      this.predictedTumor = pred[0][0];
      console.log(this.predictedTumor);
    },
  },
};
</script>

<style scoped>
#upload {
  height: 65vh;
  width: 100%;
  margin-bottom: 2rem;
  background-color: rgba(0, 0, 0, 0.1);
}

.title-description {
  height: 20%;
  width: 100%;
  background-color: #191970;
  color: #fff;
  display: flex;
  flex-direction: column;
  justify-content: space-around;
  align-items: center;
}

#title {
  font-size: 2rem;
  text-transform: uppercase;
  text-decoration: underline;
}

#description {
  width: 75%;
  text-align: center;
  font-size: 1.1rem;
}

#display {
  height: 75%;
  width: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}

#form {
  width: 50%;
  height: 80%;
  border: 2px dashed black;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0rem 5rem;
}

form {
  width: 60%;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

label {
  font-size: 1.4rem;
  color: #191970;
  font-weight: 500;
}

input[type="submit"] {
  padding: 0.5rem 1.5rem;
  font-size: 1.1rem;
  background-color: red;
  color: white;
  border: none;
  border-radius: 0.5rem;
}

input[type="submit"]:active {
  transform: scale(0.98);
}

#img-result {
  height: 80%;
  width: 80%;
  display: flex;
  justify-content: space-around;
}

.image-and-form {
  height: 100%;
  width: 40%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

#image {
  width: 25%;
  height: 35%;
  border: 1px solid yellow;
  object-fit: cover;
}

.image-and-form #form {
  height: 30%;
  width: 90%;
  border: 1px dashed black;
  display: flex;
  justify-content: space-around;
  align-items: center;
  padding: 0rem;
  margin-top: 0.2rem;
}

.image-and-form #form label {
  font-size: 0.9rem;
}

.image-and-form #form form {
  width: 55%;
  display: flex;
  justify-content: space-between;
  font-size: 0.9rem;
}

.image-and-form input[type="submit"] {
  padding: 0.2rem 0.8rem;
  font-size: 0.9rem;
  background-color: red;
  color: white;
  border: none;
  border-radius: 0.2rem;
}

.image-and-form input[type="submit"]:active {
  transform: scale(0.98);
}

svg {
  border: 1px solid black;
  border-radius: 1rem;
}
</style>