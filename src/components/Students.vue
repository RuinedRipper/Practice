<template>
  <div class="center" id="app">
    <h2 style="font-family: 'Lucida Sans Unicode', 'Lucida Grande', Sans-Serif">
      Додати учня
    </h2>
    <form @submit.prevent="createStudent()" class="student-form">
      <div class="form-group">
        <label
          style="
            font-family: 'Lucida Sans Unicode', 'Lucida Grande', Sans-Serif;
          "
          for="name"
          >Ім'я</label
        >
        <input
          type="text"
          id="name"
          name="name"
          v-model.trim="newStudent.name"
          required
        />
      </div>

      <div class="form-group">
        <label>Група</label>
        <select v-model="newStudent.group" required>
          <option value="RPZ 20 1/9">RPZ 20 1/9</option>
          <option value="RPZ 20 2/9">RPZ 20 2/9</option>
        </select>
      </div>

      <div class="form-group">
        <label for="mark">Оцінка</label>
        <input
          type="number"
          id="mark"
          name="mark"
          v-model="newStudent.mark"
          required
        />
      </div>

      <div class="form-group">
        <label for="isDonePr">Здав ПР</label>
        <input
          type="checkbox"
          id="isDonePr"
          name="isDonePr"
          v-model="newStudent.isDonePr"
        />
      </div>

      <button type="submit" class="submit-button">Додати</button>
    </form>

    <h1 style="font-family: 'Lucida Sans Unicode', 'Lucida Grande', Sans-Serif">
      Пошук
    </h1>
    <input class="filter" type="text" name="search" v-model="search" />
    <p style="font-family: 'Lucida Sans Unicode', 'Lucida Grande', Sans-Serif">
      Кількість студентів: {{ studentsCount }}
    </p>
    <table>
      <thead>
        <tr>
          <th>Ім'я</th>
          <th>Група</th>
          <th>Оцінка</th>
          <th>Здав ПР</th>
          <th></th>
          <th></th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(st, index) in students" :key="index">
          <template v-if="!st.isEdit"
            ><td>
              <router-link
                v-bind:to="'/student-info/' + st._id"
                :class="
                  st.name.includes(search) && search !== '' ? 'red' : 'black'
                "
              >
                {{ st.name }}
              </router-link>
            </td>
            <td>{{ st.group }}</td>
            <td>{{ st.mark }}</td>
            <td>
              <input type="checkbox" name="isDonePr" v-model="st.isDonePr" />
            </td>
            <td>
              <button
                href="#"
                class="delButton"
                @click="deleteStudent(st._id)"
                v-show="st.group === getCurrentUser.group"
              >
                Видалити
              </button>
            </td>
            <td>
              <button
                href="#"
                class="editButton"
                @click="editStudent(index)"
                v-show="st.group === getCurrentUser.group"
              >
                Редагувати
              </button>
            </td>
          </template>
          <template v-else>
            <td>
              <input type="text" v-model="st.updateStudent.name" />
            </td>
            <td>
              <select v-model="st.updateStudent.group">
                <option value="RPZ 20 1/9">RPZ 20 1/9</option>
                <option value="RPZ 20 2/9">RPZ 20 2/9</option>
              </select>
            </td>
            <td><input type="text" v-model="st.updateStudent.mark" /></td>
            <td>
              <input
                type="checkbox"
                name="isDonePr"
                v-model="st.updateStudent.isDonePr"
              />
            </td>
            <td>
              <button class="saveButton" @click="saveStudent(index, st._id)">
                Зберегти
              </button>
            </td>
            <td>
              <button class="editButton" @click="cancelEditStudent(index)">
                Скасувати
              </button>
            </td>
          </template>
        </tr>
      </tbody>
    </table>

    <h2 style="font-family: 'Lucida Sans Unicode', 'Lucida Grande', Sans-Serif">
      Конвертор
    </h2>
    <div class="currency-converter">
      <label for="amount">Кількість:</label>
      <input type="number" name="amount" v-model="convertor.amount" />

      <label for="fromCurrency">З валюти:</label>
      <select name="fromCurrency" v-model="convertor.fromCurrency">
        <option v-for="(c, index) in currencies" :value="c" :key="index">
          {{ c }}
        </option>
      </select>

      <label for="toCurrency">До валюти:</label>
      <select name="toCurrency" v-model="convertor.toCurrency">
        <option v-for="(c, index) in currencies" :value="c" :key="index">
          {{ c }}
        </option>
      </select>

      <button @click="convert(convertor)">Конвертувати</button>
      <label style="margin-top: 10px"
        >Конвертована валюта: {{ converterCurrency }}</label
      >
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, computed } from "vue";
import { useStore } from "vuex";
import axios from "axios";
import cc from "currency-codes";

const store = useStore();
const students = ref([]);
const search = ref("");
const newStudent = ref({ name: "", group: "", mark: "", isDonePr: false });
const updateStudent = ref({
  name: "",
  group: "",
  mark: "",
  isDonePr: false,
});
const currencies = ref(["UAH", "EUR", "USD"]);
const convertor = ref({
  amount: "",
  fromCurrency: "",
  toCurrency: "",
});
const converterCurrency = ref("");

const fetchStudents = () => {
  axios.get("http://34.82.81.113:3000/students").then((response) => {
    students.value = response.data.map((st) => ({
      ...st,
      isEdit: false,
    }));
    store.commit("setCount", students.value.length);
  });
};

const studentsCount = computed(() => {
  return store.getters.getCount;
});

const getCurrentUser = computed(() => {
  return store.getters.getUser;
});

const deleteStudent = (id) => {
  axios.delete(`http://34.82.81.113:3000/students/${id}`).then(() => {
    students.value = students.value.filter((st) => st._id !== id);
  });
};

const editStudent = (index) => {
  const { name, group, mark, isDonePr } = students.value[index];
  students.value[index].updateStudent = { name, group, mark, isDonePr };
  students.value[index].isEdit = true;
};

const cancelEditStudent = (index) => {
  students.value[index].isEdit = false;
};

const saveStudent = (index, id) => {
  const { name, group, mark, isDonePr } = students.value[index].updateStudent;
  if (name && group && mark) {
    axios
      .put(`http://34.82.81.113:3000/students/${id}`, {
        name,
        group,
        mark,
        isDonePr,
      })
      .then((response) => {
        students.value[index] = {
          ...response.data,
          isEdit: false,
        };
      });
  }
};

const createStudent = () => {
  axios
    .post("http://34.82.81.113:3000/students", newStudent.value)
    .then((response) => {
      students.value.push({ ...response.data, isEdit: false });
      newStudent.value.name = "";
      newStudent.value.group = "";
      newStudent.value.mark = "";
      newStudent.value.isDonePr = false;
    });
};

const convert = async ({ amount, fromCurrency, toCurrency }) => {
  if (amount !== "" && fromCurrency !== "" && toCurrency !== "") {
    if (fromCurrency === toCurrency) {
      converterCurrency.value = parseFloat(amount).toFixed(2);
    } else {
      const { data } = await axios.get("https://api.monobank.ua/bank/currency");
      if (data) {
        const fromCurrencyCode = cc.code(fromCurrency);
        const toCurrencyCode = cc.code(toCurrency);
        if (Number(toCurrencyCode.number) === 980) {
          const CurrencyRate = data.find(
            (rate) =>
              rate.currencyCodeA === Number(fromCurrencyCode.number) &&
              rate.currencyCodeB === Number(toCurrencyCode.number)
          );
          converterCurrency.value = (amount * CurrencyRate.rateBuy).toFixed(2);
        } else if (Number(fromCurrencyCode.number) === 980) {
          const CurrencyRate = data.find(
            (rate) =>
              rate.currencyCodeA === Number(toCurrencyCode.number) &&
              rate.currencyCodeB === 980
          );
          converterCurrency.value = (amount / CurrencyRate.rateSell).toFixed(2);
        } else {
          const fromCurrencyRate = data.find(
            (rate) =>
              rate.currencyCodeA === Number(fromCurrencyCode.number) &&
              rate.currencyCodeB === 980
          );
          const toCurrencyRate = data.find(
            (rate) =>
              rate.currencyCodeA === Number(toCurrencyCode.number) &&
              rate.currencyCodeB === 980
          );
          converterCurrency.value = (
            (amount * fromCurrencyRate.rateBuy) /
            toCurrencyRate.rateSell
          ).toFixed(2);
        }
      }
    }
  }
};

onMounted(() => {
  fetchStudents();
});
</script>

<style scoped>
.light-theme {
  background-color: #f0f0f0;
  color: #333;
  border: 2px solid #ccc;
  border-radius: 5px;
  padding: 10px 20px;
  font-size: 16px;
  margin-bottom: 20px;
  font-family: "Lucida Sans Unicode", "Lucida Grande", "Sans-Serif";
}

.light-theme:hover {
  background-color: #ccc;
  color: #333;
}

.dark-theme {
  background-color: #333;
  color: #fff;
  border: 2px solid #555;
  border-radius: 5px;
  padding: 10px 20px;
  font-size: 16px;
  margin-bottom: 20px;
  font-family: "Lucida Sans Unicode", "Lucida Grande", "Sans-Serif";
}

.dark-theme:hover {
  background-color: #555;
  color: #fff;
}

.center {
  text-align: center;
}

.red {
  color: red;
}

.black {
  color: black;
}

table {
  margin-top: 20px;
  font-family: "Lucida Sans Unicode", "Lucida Grande", Sans-Serif;
  text-align: center;
  border-collapse: collapse;
  border-spacing: 5px;
  background: #e1e3e0;
  border-radius: 20px;
  margin: 0 auto;
}

th {
  font-size: 22px;
  font-weight: 300;
  padding: 12px 10px;
  border-bottom: 2px solid #f56433;
  color: #f56433;
}

td {
  padding: 10px;
  color: #8d8173;
}

.delButton {
  background-color: #ff1100;
  color: white;
  border: none;
  padding: 8px 12px;
  border-radius: 10px;
  font-family: "Lucida Sans Unicode", "Lucida Grande", Sans-Serif;
}

.delButton:hover {
  background-color: #ac1616;
  cursor: pointer;
}

.editButton {
  background-color: #2474eb;
  color: white;
  border: none;
  padding: 8px 12px;
  border-radius: 10px;
  font-family: "Lucida Sans Unicode", "Lucida Grande", Sans-Serif;
}

.saveButton {
  background-color: #0eca17;
  color: white;
  border: none;
  padding: 8px 12px;
  border-radius: 10px;
  font-family: "Lucida Sans Unicode", "Lucida Grande", Sans-Serif;
}

.saveButton:hover {
  background-color: #189b1e;
  cursor: pointer;
}

.editButton:hover {
  background-color: #215dcc;
  cursor: pointer;
}

.filter {
  margin-bottom: 20px;
}

.student-form {
  width: 350px;
  margin: 0 auto;
  padding: 30px;
  background-color: #f0f0f0;
  border: 2px solid #000000;
  border-radius: 10px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.4);
}

.form-group {
  margin: 15px 0;
}

.form-group label {
  display: block;
  font-weight: bold;
  color: #000000;
  font-size: 14px;
  font-family: "Lucida Sans Unicode", "Lucida Grande", Sans-Serif;
}

.form-group input[type="text"],
.form-group input[type="number"] {
  width: 90%;
  padding: 12px;
  border: 2px solid #000000;
  border-radius: 6px;
  box-shadow: 0 0 5px rgba(0, 0, 0, 0.2);
  transition: border-color 0.3s;
}

.form-group input[type="checkbox"] {
  margin-right: 10px;
}

.form-group select {
  width: 98%;
  padding: 12px;
  border: 2px solid #000000;
  border-radius: 6px;
  box-shadow: 0 0 5px rgba(0, 0, 0, 0.2);
  transition: border-color 0.3s;
}

.submit-button {
  display: block;
  width: 100%;
  padding: 15px;
  background-color: #007bff;
  color: #fff;
  border: none;
  border-radius: 10px;
  transition: background-color 0.3s;
  font-family: "Lucida Sans Unicode", "Lucida Grande", Sans-Serif;
  font-size: 20px;
}

.submit-button:hover {
  background-color: #0056b3;
  cursor: pointer;
}

.currency-converter {
  width: 350px;
  margin: 0 auto;
  padding: 30px;
  background-color: #f0f0f0;
  border: 2px solid #000000;
  border-radius: 10px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.4);
}

.currency-converter label {
  display: block;
  font-weight: bold;
  color: #000000;
  font-size: 14px;
  font-family: "Lucida Sans Unicode", "Lucida Grande", Sans-Serif;
}

.currency-converter select {
  width: 85%;
  padding: 12px;
  border: 2px solid #000000;
  border-radius: 6px;
  box-shadow: 0 0 5px rgba(0, 0, 0, 0.2);
  transition: border-color 0.3s;
  margin-bottom: 15px;
}

.currency-converter input {
  width: 80%;
  padding: 12px;
  border: 2px solid #000000;
  border-radius: 6px;
  box-shadow: 0 0 5px rgba(0, 0, 0, 0.2);
  transition: border-color 0.3s;
  margin-bottom: 15px;
}

.currency-converter button {
  font-family: "Lucida Sans Unicode", "Lucida Grande", Sans-Serif;
  background-color: #007bff;
  color: #fff;
  width: 90%;
  border: none;
  padding: 10px 20px;
  border-radius: 5px;
  transition: background-color 0.3s;
}

.currency-converter button:hover {
  background-color: #0261c7;
  cursor: pointer;
}
</style>
<style scoped>
.light-theme {
  background-color: #f0f0f0;
  color: #333;
  border: 2px solid #ccc;
  border-radius: 5px;
  padding: 10px 20px;
  font-size: 16px;
  margin-bottom: 20px;
  font-family: "Lucida Sans Unicode", "Lucida Grande", "Sans-Serif";
}

.light-theme:hover {
  background-color: #ccc;
  color: #333;
}

.dark-theme {
  background-color: #333;
  color: #fff;
  border: 2px solid #555;
  border-radius: 5px;
  padding: 10px 20px;
  font-size: 16px;
  margin-bottom: 20px;
  font-family: "Lucida Sans Unicode", "Lucida Grande", "Sans-Serif";
}

.dark-theme:hover {
  background-color: #555;
  color: #fff;
}

.center {
  text-align: center;
}

.red {
  color: red;
}

.black {
  color: black;
}

table {
  margin-top: 20px;
  font-family: "Lucida Sans Unicode", "Lucida Grande", Sans-Serif;
  text-align: center;
  border-collapse: collapse;
  border-spacing: 5px;
  background: #e1e3e0;
  border-radius: 20px;
  margin: 0 auto;
}

th {
  font-size: 22px;
  font-weight: 300;
  padding: 12px 10px;
  border-bottom: 2px solid #f56433;
  color: #f56433;
}

td {
  padding: 10px;
  color: #8d8173;
}

.delButton {
  background-color: #ff1100;
  color: white;
  border: none;
  padding: 8px 12px;
  border-radius: 10px;
  font-family: "Lucida Sans Unicode", "Lucida Grande", Sans-Serif;
}

.delButton:hover {
  background-color: #ac1616;
  cursor: pointer;
}

.editButton {
  background-color: #2474eb;
  color: white;
  border: none;
  padding: 8px 12px;
  border-radius: 10px;
  font-family: "Lucida Sans Unicode", "Lucida Grande", Sans-Serif;
}

.saveButton {
  background-color: #0eca17;
  color: white;
  border: none;
  padding: 8px 12px;
  border-radius: 10px;
  font-family: "Lucida Sans Unicode", "Lucida Grande", Sans-Serif;
}

.saveButton:hover {
  background-color: #189b1e;
  cursor: pointer;
}

.editButton:hover {
  background-color: #215dcc;
  cursor: pointer;
}

.filter {
  margin-bottom: 20px;
}

.student-form {
  width: 350px;
  margin: 0 auto;
  padding: 30px;
  background-color: #f0f0f0;
  border: 2px solid #000000;
  border-radius: 10px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.4);
}

.form-group {
  margin: 15px 0;
}

.form-group label {
  display: block;
  font-weight: bold;
  color: #000000;
  font-size: 14px;
  font-family: "Lucida Sans Unicode", "Lucida Grande", Sans-Serif;
}

.form-group input[type="text"],
.form-group input[type="number"] {
  width: 90%;
  padding: 12px;
  border: 2px solid #000000;
  border-radius: 6px;
  box-shadow: 0 0 5px rgba(0, 0, 0, 0.2);
  transition: border-color 0.3s;
}

.form-group input[type="checkbox"] {
  margin-right: 10px;
}

.form-group select {
  width: 98%;
  padding: 12px;
  border: 2px solid #000000;
  border-radius: 6px;
  box-shadow: 0 0 5px rgba(0, 0, 0, 0.2);
  transition: border-color 0.3s;
}

.submit-button {
  display: block;
  width: 100%;
  padding: 15px;
  background-color: #007bff;
  color: #fff;
  border: none;
  border-radius: 10px;
  transition: background-color 0.3s;
  font-family: "Lucida Sans Unicode", "Lucida Grande", Sans-Serif;
  font-size: 20px;
}

.submit-button:hover {
  background-color: #0056b3;
  cursor: pointer;
}

.currency-converter {
  width: 350px;
  margin: 0 auto;
  padding: 30px;
  background-color: #f0f0f0;
  border: 2px solid #000000;
  border-radius: 10px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.4);
}

.currency-converter label {
  display: block;
  font-weight: bold;
  color: #000000;
  font-size: 14px;
  font-family: "Lucida Sans Unicode", "Lucida Grande", Sans-Serif;
}

.currency-converter select {
  width: 85%;
  padding: 12px;
  border: 2px solid #000000;
  border-radius: 6px;
  box-shadow: 0 0 5px rgba(0, 0, 0, 0.2);
  transition: border-color 0.3s;
  margin-bottom: 15px;
}

.currency-converter input {
  width: 80%;
  padding: 12px;
  border: 2px solid #000000;
  border-radius: 6px;
  box-shadow: 0 0 5px rgba(0, 0, 0, 0.2);
  transition: border-color 0.3s;
  margin-bottom: 15px;
}

.currency-converter button {
  font-family: "Lucida Sans Unicode", "Lucida Grande", Sans-Serif;
  background-color: #007bff;
  color: #fff;
  width: 90%;
  border: none;
  padding: 10px 20px;
  border-radius: 5px;
  transition: background-color 0.3s;
}

.currency-converter button:hover {
  background-color: #0261c7;
  cursor: pointer;
}
</style>
