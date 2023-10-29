<template>
  <div
    :class="
      store.state.selectedTheme === 'light' ? 'light-theme' : 'dark-theme'
    "
  >
    <router-link v-bind:to="'/'" class="mainLink">
      <h1 :class="store.state.selectedTheme === 'light' ? 'white' : 'black'">
        Студенти
      </h1>
    </router-link>
    <button
      :class="
        store.state.selectedTheme === 'light'
          ? 'button-light-theme'
          : 'button-dark-theme'
      "
      @click="toggleTheme"
    >
      Змінити тему
    </button>
    <router-view></router-view>
  </div>
  <div
    style="height: 100vh"
    :class="
      store.state.selectedTheme === 'light' ? 'light-theme' : 'dark-theme'
    "
  ></div>
</template>

<script setup>
import { useStore } from "vuex";
const store = useStore();

const toggleTheme = () => {
  const newTheme = store.state.selectedTheme === "light" ? "dark" : "light";
  store.commit("setTheme", newTheme);
};
</script>

<style>
body {
  margin: 0;
  padding: 0;
}

html {
  margin: 0;
  padding: 0;
}

.mainLink {
  color: black;
  text-decoration-line: none;
}

.light-theme {
  background-color: #fff;
  color: #333;
  padding: 10px 20px;
  font-size: 16px;
}

.dark-theme {
  background-color: #333;
  color: #fff;
  padding: 10px 20px;
  font-size: 16px;
}

.black {
  color: #fff;
  text-align: center;
  font-family: "Lucida Sans Unicode", "Lucida Grande", "Sans-Serif";
}

.white {
  color: #333;
  text-align: center;
  font-family: "Lucida Sans Unicode", "Lucida Grande", "Sans-Serif";
}

.button-light-theme {
  background-color: #f0f0f0;
  color: #333;
  border: 2px solid #ccc;
  border-radius: 5px;
  padding: 10px 20px;
  font-size: 16px;
  display: block;
  margin: 0 auto;
  margin-bottom: 20px;
}

.button-light-theme:hover {
  background-color: #ccc;
  color: #333;
}

.button-dark-theme {
  background-color: #333;
  color: #fff;
  border: 2px solid #555;
  border-radius: 5px;
  padding: 10px 20px;
  font-size: 16px;
  display: block;
  margin: 0 auto;
  margin-bottom: 20px;
}

.button-dark-theme:hover {
  background-color: #555;
  color: #fff;
}
</style>
