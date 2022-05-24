<template>
  <section id="results">
    <div class="title-description">
      <div id="title">{{ title }}</div>
      <div id="description">{{ description }}</div>
    </div>
    <div id="res">
      <div id="display">
        <div id="select-factor">
          <label for="choose-factor">Select Factor:</label>
          <select name="choose-factor" @change="setYAttribute($event)">
            <option
              v-for="item in factors"
              :key="item.value"
              :value="item.value"
              :selected="yAttribute === item.value"
            >
              {{ item.label }}
            </option>
          </select>
        </div>
        <div id="factor-def">{{ factorDef[yAttribute] }}</div>
        <div id="algorithm-def-table">
          <div id="algorithmDef"></div>
          <table id="tab"></table>
        </div>
      </div>
      <div id="graph">
        <svg :width="width" :height="height">
          <g
            v-if="data.length"
            :style="{
              transform:
                'translate(' + margin.left + 'px, ' + margin.top + 'px)',
            }"
          >
            <g>
              <line :y2="innerHeight" stroke="#000" />
              <g
                v-for="tickValue in yScale.ticks()"
                :key="tickValue"
                :style="{
                  transform: 'translate(-3px, ' + yScale(tickValue) + 'px)',
                }"
              >
                <line x2="6" stroke="#222" />
                <text style="text-anchor: end" dy="0.32em" dx="-0.32em">
                  {{ tickValue }}
                </text>
              </g>
            </g>
            <g :style="{ transform: 'translate(0px, ' + innerHeight + 'px)' }">
              <line :x2="innerWidth" stroke="#000" />
              <g
                v-for="tickValue in xScale.domain()"
                :key="tickValue"
                :style="{
                  transform: 'translate(' + xScale(tickValue) + 'px, 3px)',
                }"
              >
                <line y1="-6" stroke="#222" />
                <text style="text-anchor: middle" dy="0.81em">
                  {{ tickValue }}
                </text>
              </g>
            </g>

            <g
              v-for="(d, index) in data"
              :key="d.algorithm"
              :style="{
                transform:
                  'translate(' +
                  xScale(xValue(d)) +
                  'px, ' +
                  yScale(yValue(d)) +
                  'px )',
              }"
              @mouseenter="mouseEnter(d, index)"
              @mouseleave="mouseLeave"
              :opacity="hoveredLegend ? 0.1 : 1"
            >
              <rect
                :x="-xScale.bandwidth() / 2"
                :width="xScale.bandwidth()"
                :height="innerHeight - yScale(yValue(d))"
                :fill="d.color"
              />
            </g>
            { hoveredLegend &&
            <g
              v-for="(d, index) in data"
              :key="d.algorithm"
              :style="{
                transform:
                  'translate(' +
                  xScale(xValue(d)) +
                  'px, ' +
                  yScale(yValue(d)) +
                  'px )',
              }"
              @mouseenter="mouseEnter(d, index)"
              @mouseleave="mouseLeave"
            >
              <rect
                :x="-xScale.bandwidth() / 2"
                :width="xScale.bandwidth()"
                :height="innerHeight - yScale(yValue(d))"
                :fill="d.color == hoveredLegend ? d.color : 'transparent'"
              />
            </g>
            }

            <g
              :style="{
                transform:
                  'translate(' + innerWidth + 'px, ' + margin.top * 2 + 'px)',
              }"
            >
              <g
                @mouseenter="selectLegend('#00f')"
                @mouseleave="unselectLegend"
              >
                <rect fill="blue" width="20" height="20" />
                <text dx="25px" dy="0.81em">Efficient</text>
              </g>
              <g
                style="transform: translate(0px, 30px)"
                @mouseenter="selectLegend('#f00')"
                @mouseleave="unselectLegend"
              >
                <rect fill="red" width="20" height="20" />
                <text dx="25px" dy="0.81em">Inefficient</text>
              </g>
            </g>
          </g>
        </svg>
        <div id="tooltip"></div>
      </div>
    </div>
  </section>
</template>

<script>
import { scaleLinear, scaleBand, max, select, mean } from "d3";

const url =
  "https://gist.githubusercontent.com/SiddharthMittal07/3c7afd2a88faa690291f9474b60483b6/raw/f2977fdbd8bf4fbd78878e773582ef75f75340ac/major_project_algorithms.json";

export default {
  name: "Results",
  data() {
    return {
      title: "Results",
      description:
        "We have used 6 Supervised Machine Learning Algorithms, namely: Support Vector Machine, Decision Tree Classification, Logistic Regression, Naive Bayes Classification, Random Forest Algorithm, and K-Nearest Neighbors. The results are observed on the basis of 4 metrics of Precision, Recall, Accuracy and Processing Time.",
      scaleFactor: 0.47,
      margin: { top: 40, bottom: 50, left: 40, right: 120 },
      factors: [
        { value: "testing_accuracy", label: "ACCURACY" },
        { value: "time", label: "TIME" },
        { value: "precision_score", label: "PRECISION" },
        { value: "recall_score", label: "RECALL" },
      ],
      factorDef: {
        testing_accuracy:
          "Accuracy is defined as the percentage of correct predictions for the test data. It can be calculated easily by dividing the number of correct predictions by the number of total predictions.",
        time: "Time complexity can be seen as the measure of how fast or slow an algorithm will perform for the input size. Time complexity is always given with respect to some input size (say n).",
        precision_score:
          "Precision is one indicator of a machine learning model's performance – the quality of a positive prediction made by the model. Precision refers to the number of true positives divided by the total number of positive predictions.",
        recall_score:
          "Recall literally is how many of the true positives were recalled (found), i.e. how many of the correct hits were also found.",
      },
      yAttribute: "testing_accuracy",
      xValue: (d) => d.algorithm,
      yValue: (d) => d[this.yAttribute],
      algorithmDef: {
        LogisticRegression: `It is a statistical analysis method to predict a categorical outcome based on prior observations of a dataset. The model predicts a dependent data variable by analysing the relationship between one or more existing independent variables.
    Logistic Regression has become an important tool in the discipline of machine learning. It allows algorithms used in machine learning applications to classify incoming data based on historical data. As additional relevant data comes in, the algorithms get better at predicting classifications within datasets.`,
        SVM: `Support Vector Machine or SVM is one of the most popular Supervised learning algorithms, which is used for classification as well as regression problems. The goal of the SVM algorithm is to create the best line or decision boundary that can segregate n-dimensional space into classes so that we can  easily put the new data point in the correct category. This best decision boundary is called a hyperplane.`,
        KNN: `KNN algorithm assumes the similarity between the new case/data and available cases and put the new case into the category that is most similar to the available categories. KNN algorithm stores all the available data and classifies a new data point based on the similarity. This means when new data appears then it can be easily classified into a well suited category by using the algorithm.`,
        NaiveBayes: `Naive Bayes algorithm is a supervised learning algorithm, which is based on Bayes theorem and used for solving classification problems. It is mainly used in text classification that includes a high-dimensional training data. It is one of the simple and most effective classification algorithms which helps in building fast machine learning models that can make quick predictions.
    It is called “Naive” because it assumes that the occurrence of a certain feature is independent of the occurrence of other features. It is called “Bayes” because it depends on the principle of Bayes’ Theorem.`,
        DecisionTree: `It is a Tree-Structured classifier, where internal nodes represent the features of a dataset, branches represent the decision rules and each leaf node represents the outcome. It is a graphical representation for getting all the possible solutions to a problem/decision based on given conditions.`,
        RandomForest: `Random Forest algorithm is based on the concept of ensemble learning, which is a process of combining multiple classifiers to solve a complex problem and to improve the performance of the model. Random Forest is a classifier that contains a number of decision trees on various subsets of the given dataset and takes the average to improve the predictive accuracy of that dataset. The greater number of trees in the forest leads to higher accuracy and prevents the problem of over fitting.`,
      },
      data: [],
      hoveredLegend: "",
    };
  },
  computed: {
    width() {
      return this.scaleFactor * window.innerWidth;
    },
    height() {
      return this.scaleFactor * window.innerHeight;
    },
    innerWidth() {
      return this.width - this.margin.left - this.margin.right;
    },
    innerHeight() {
      return this.height - this.margin.top - this.margin.bottom;
    },
  },
  methods: {
    setYAttribute(e) {
      this.yAttribute = e.target.value;

      const maxValue = max(this.data, this.yValue);
      const meanValue = mean(this.data, this.yValue);

      this.xScale = scaleBand()
        .domain(this.data.map((d) => this.xValue(d)))
        .range([0, this.innerWidth])
        .padding(0.5);
      this.yScale = scaleLinear()
        .domain([0, maxValue])
        .range([this.innerHeight, 0]);

      this.data.map((d) => {
        if (maxValue < 1) {
          if (this.yValue(d) > meanValue) {
            d.color = "#00f";
          } else {
            d.color = "#f00";
          }
        } else {
          if (this.yValue(d) < meanValue) {
            d.color = "#00f";
          } else {
            d.color = "#f00";
          }
        }
      });
    },

    mouseEnter(data, i) {
      this.factors.forEach(({ value, label }) => {
        if (value === this.yAttribute) {
          select("#tooltip")
            .append("span")
            .text(label + ": " + data[value].toFixed(3))
            .style("font-weight", "bold");
        } else {
          select("#tooltip")
            .append("span")
            .text(label + ": " + data[value].toFixed(3));
        }
      });
      select("#tooltip").style("top", this.height / 2 + "px");
      select("#tooltip").style("left", ((i + 1) * 120).toString() + "px");
      select("#tooltip").style("visibility", "visible");

      select("#algorithmDef")
        .text(this.algorithmDef[data.algorithm])
        .style("display", "block");
    },

    mouseLeave() {
      select("#tooltip").selectAll("span").remove();
      select("#tooltip").style("visibility", "hidden");
      select("#algorithmDef").text("").style("display", "none");
    },

    selectLegend(color) {
      this.hoveredLegend = color;
      const filteredData = this.data.filter((data) => data.color === color);
      const table = select("#tab");
      const thead = table.append("tr");
      table.style("width", "100%");
      filteredData.map((data) =>
        thead
          .append("th")
          .text(this.xValue(data))
          .style("text-align", "center")
          .style("border", "1px solid black")
      );
      thead
        .append("th")
        .text("Average")
        .style("text-align", "center")
        .style("border", "1px solid black");
      const tbody = table.append("tr");
      filteredData.map((data) =>
        tbody
          .append("td")
          .text(this.yValue(data).toFixed(2))
          .style("background-color", color)
          .style("color", "white")
          .style("text-align", "center")
      );
      tbody
        .append("td")
        .text(mean(this.data, this.yValue).toFixed(2))
        .style("background-color", "green")
        .style("color", "white")
        .style("text-align", "center");

      table.style("visibility", "visible");
    },

    unselectLegend() {
      this.hoveredLegend = null;

      select("#tab").selectAll("tr").remove();
      select("#tab").selectAll("th").remove();
      select("#tab").selectAll("td").remove();
      select("#tab").style("visibility", "hidden");
    },
  },
  async mounted() {
    const response = await fetch(url);
    this.data = await response.json();

    const maxValue = max(this.data, this.yValue);
    const meanValue = mean(this.data, this.yValue);

    this.xScale = scaleBand()
      .domain(this.data.map((d) => this.xValue(d)))
      .range([0, this.innerWidth])
      .padding(0.5);
    this.yScale = scaleLinear()
      .domain([0, maxValue])
      .range([this.innerHeight, 0]);

    this.data.map((d) => {
      if (maxValue < 1) {
        if (this.yValue(d) > meanValue) {
          d.color = "#00f";
        } else {
          d.color = "#f00";
        }
      } else {
        if (this.yValue(d) < meanValue) {
          d.color = "#00f";
        } else {
          d.color = "#f00";
        }
      }
    });
  },
};
</script>

<style scoped>
#results {
  height: 75vh;
  width: 100%;
  margin-bottom: 2rem;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  background-color: #eef;
}

.title-description {
  height: 18%;
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

#res {
  height: 80%;
  width: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}

#display {
  height: 80%;
  width: 40%;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  align-items: center;
}

#select-factor {
  height: 15%;
  width: 60%;
  display: flex;
  justify-content: center;
  align-items: center;
}

#select-factor label {
  font-size: 1.7rem;
  color: #191970;
}

#select-factor select {
  font-size: 1.35rem;
  padding: 0.5rem 1.5rem;
  margin-left: 1rem;
}

#factor-def {
  height: 25%;
  width: 90%;
  border: 2px solid #191970;
  background-color: #fff;
  padding: 0.5rem;
  border-radius: 1rem;
  font-size: 1.1rem;
}

#algorithm-def-table {
  height: 40%;
  width: 90%;
  position: relative;
}

#algorithmDef {
  position: absolute;
  max-height: 90%;
  width: 100%;
  padding: 0.5rem;
  background-color: #fff;
  border: 2px solid #191970;
  border-radius: 0.6rem;
  display: none;
  top: 0;
  left: 0;
  overflow: hidden;
  transition: display 150ms ease-in-out;
}

svg {
  background-color: #fff;
  margin-left: 2rem;
  cursor: pointer;
}

#graph {
  position: relative;
}

#tooltip {
  position: absolute;
  padding: 0.5rem;
  font-size: 0.8rem;
  background-color: #fff;
  border: 1px solid blue;
  border-radius: 0.5rem;
  display: flex;
  flex-direction: column;
  visibility: hidden;
  cursor: none;
}

#tab {
  position: absolute;
  background-color: white;
  border: 0.25rem solid #f8d76e;
  width: 90%;
  height: 50%;
  color: black;
  font-size: 1.2rem;
  transition: display 150ms ease-in-out;
  visibility: hidden;
}

#tab td,
#tab th {
  border: 0.0625rem solid #000;
  text-align: center;
}
</style>

