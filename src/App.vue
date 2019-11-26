<template>
  <div id="app" class="wrapper">
    <div class="header">
      <Header />
    </div>
    <div class="content">
      <DragonGameBoard msg="Welcome to Your Vue.js App" />
    </div>
  </div>
</template>

<script>
import Header from "./components/Header";
import DragonGameBoard from "./components/DragonGameBoard.vue";

export default {
  name: "app",
  data: () => {
    return {
      imgUrl: "/assets/dragon-logo.png"
    };
  },
  components: {
    Header,
    DragonGameBoard
  }
};
</script>

<style>
html,
body {
  height: 100vh;
  width: 100vw;
}
html {
  background-size: cover;
  background-color: #000;
  background-repeat: no-repeat;
  background-image: url("./assets/background.jpg");
}
body {
  background-color: rgba(0, 0, 0, 0.6);
}
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
.wrapper {
  width: 100vw;
  height: 100vh;
  display: flex;
  flex-direction: column;
}

.header {
  height: 100px;
}

.content {
  flex: 1;
}
</style>
