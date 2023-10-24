<template>
  <div>
    <router-link v-bind:to="'/'" class="mainLink">
      <h1
        class="center"
        style="font-family: 'Lucida Sans Unicode', 'Lucida Grande', Sans-Serif"
      >
        Студенти
      </h1>
    </router-link>
    <router-view></router-view>
  </div>
</template>

<style scoped>
.center {
  text-align: center;
}
.mainLink {
  color: black;
  text-decoration-line: none;
}
</style>
