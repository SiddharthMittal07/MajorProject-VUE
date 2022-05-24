<template>
  <footer>
    <div class="container">
      <ul id="links">
        <li><a href="#" target="_blank">Dataset</a></li>
        <li>
          <a href="#" target="_blank">Jupyter Notebooks of all Algorithms</a>
        </li>
        <li>
          <a href="#" target="_blank">Result Statistics for Infographics</a>
        </li>
        <li>
          <a href="#" target="_blank">Algorithm Definitions and Content</a>
        </li>
      </ul>
      <div id="motivation">
        <div id="title-description">
          <div id="title">Motivation</div>
          <div id="description">
            Lorem ipsum dolor sit amet consectetur adipisicing elit. Dicta
            beatae obcaecati labore fugit, commodi laudantium magnam recusandae
            vero officia modi?
          </div>
        </div>
        <div id="socials">
          <span>Instagram</span>
          <span>Facebook</span>
          <span>Twitter</span>
        </div>
        <a href="#" target="_blank">API</a>
      </div>
      <div id="members">
        <div id="group">
          <span>Members:</span>
          <ul>
            <li><a href="#">Siddharth Mittal</a></li>
            <li><a href="#">Siddharth Mittal</a></li>
            <li><a href="#">Siddharth Mittal</a></li>
            <li><a href="#">Siddharth Mittal</a></li>
          </ul>
        </div>
      </div>
    </div>
  </footer>
</template>

<script>
export default {
  name: "Footer",
};
</script>

<style scoped>
footer {
  height: 35vh;
  width: 100%;
  background-color: #191970;
  border-top-left-radius: 2rem;
  border-top-right-radius: 2rem;
  display: flex;
  justify-content: center;
  align-items: flex-end;
}

.container {
  width: 95%;
  height: 90%;
  display: flex;
  justify-content: space-between;
}

#links {
  height: 80%;
  width: 20%;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}

#links a {
  color: #fff;
  font-size: 1.1rem;
}

#links a:hover {
  text-decoration: underline;
}

#motivation {
  height: 80%;
  width: 45%;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  align-items: center;
}

#motivation #title-description {
  width: 80%;
  text-align: center;
  color: #fff;
}

#motivation #title-description #title {
  font-size: 1.1rem;
  color: #fff;
  text-decoration: underline;
  margin-bottom: 0.2rem;
}

#motivation #socials {
  width: 80%;
  display: flex;
  justify-content: space-evenly;
  align-items: center;
  color: #fff;
  cursor: pointer;
}

#motivation a {
  color: #fff;
  font-size: 1.2rem;
}

#motivation a:hover {
  text-decoration: underline;
}

#members {
  height: 100%;
  width: 25%;
  display: flex;
  justify-content: flex-end;
  padding: 0rem 1rem;
}

#members #group {
  height: 60%;
  width: 50%;
}

#members #group span {
  font-size: 1.2rem;
  color: #fff;
  margin-bottom: 0.2rem;
}

#members #group li {
  margin: 0.5rem 0;
}

#members #group a {
  font-size: 1.1rem;
  color: #fff;
  cursor: pointer;
  padding: 0.2rem 0.4rem;
  border-radius: 0.2rem;
}

#members #group a:hover {
  background-color: #fff;
  color: #191970;
}
</style>