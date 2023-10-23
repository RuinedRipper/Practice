<template>
  <div id="app">
    <div class="form-container">
      <div class="form-group">
        <label for="city">Місто</label>
        <select v-model="selectedCity" id="city" class="custom-select">
          <option
            v-for="(city, index) in cities"
            :key="index"
            :value="city.description"
          >
            {{ city.description }}
          </option>
        </select>
      </div>
      <div class="form-group">
        <label for="branch">Відділення</label>
        <select v-model="selectedBranch" id="branch" class="custom-select">
          <option
            v-for="(branch, index) in branches"
            :key="index"
            :value="branch.description"
          >
            {{ branch.description }}
          </option>
        </select>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, onMounted, watch } from "vue";
import axios from "axios";

export default {
  setup() {
    const cities = ref([]);
    const selectedCity = ref("");
    const branches = ref([]);
    const selectedBranch = ref("");

    onMounted(() => {
      axios
        .post("https://api.novaposhta.ua/v2.0/json/", {
          apiKey: "4eca349c7b2d2423a63343ba6fe65994",
          modelName: "Address",
          calledMethod: "getCities",
          methodProperties: {},
        })
        .then((response) => {
          cities.value = response.data.data.map((city) => ({
            description: city.Description,
          }));
        });
    });

    watch(selectedCity, (newCity) => {
      if (newCity) {
        axios
          .post("https://api.novaposhta.ua/v2.0/json/", {
            apiKey: "4eca349c7b2d2423a63343ba6fe65994",
            modelName: "AddressGeneral",
            calledMethod: "getWarehouses",
            methodProperties: {
              CityName: newCity,
            },
          })
          .then((response) => {
            branches.value = response.data.data.map((branch) => ({
              description: branch.Description,
            }));
          });
      } else {
        branches.value = [];
      }
    });

    return {
      cities,
      selectedCity,
      branches,
      selectedBranch,
    };
  },
};
</script>

<style scoped>
.custom-select {
  width: 100%;
  padding: 12px;
  border: 1px solid #ccc;
  border-radius: 5px;
  background-color: #f5f5f5;
  font-size: 16px;
  color: #333;
  transition: border-color 0.2s;
}

.custom-select:hover {
  border-color: #007bff;
}

.form-container {
  max-width: 400px;
  margin: 0 auto;
  padding: 20px;
  border: 1px solid #ddd;
  border-radius: 5px;
  background-color: #fff;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.form-group {
  margin-bottom: 20px;
}

label {
  font-weight: bold;
  font-size: 18px;
  color: #333;
}
</style>
