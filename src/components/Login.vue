<template>
  <div class="login-container">
    <input v-model="input" type="text" placeholder="Ім'я студента" required />
    <button @click="login">Увійти</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      input: "",
    };
  },
  methods: {
    login() {
      this.axios.get("http://34.82.81.113:3000/students").then((res) => {
        console.log("res.data :>> ", res.data);
      });
      this.axios
        .get("http://34.82.81.113:3000/students/name/" + this.input)
        .then((response) => {
          if (response.data === null || response.data.name == "CastError") {
            return;
          }
          this.$store.commit("setUser", response.data);
          this.$router.push("/");
        });
    },
  },
};
</script>

<style scoped>
.login-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.login-container input {
  margin-bottom: 10px;
  padding: 10px;
  border-radius: 5px;
  border: 1px solid #ccc;
}

.login-container button {
  padding: 10px 20px;
  border-radius: 5px;
  border: none;
  background-color: #007bff;
  color: white;
}
</style>
