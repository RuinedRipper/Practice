<template>
  <div class="student-details">
    <div v-if="loading" class="loading-message">Завантаження...</div>
    <div class="content" v-else>
      <div class="student-profile">
        <img
          @click="isOpen = true"
          v-if="student.photo"
          :src="student.photo"
          :alt="student.name"
          class="student-photo"
        />
        <Modal :open="isOpen" @close="isOpen = !isOpen">
          <img v-if="student.photo" :src="student.photo" :alt="student.name" />
        </Modal>
      </div>
      <div class="student-info">
        <div v-if="student.name" class="info-item">
          <strong>Ім'я:</strong> {{ student.name }}
        </div>
        <div v-if="student.group" class="info-item">
          <strong>Група:</strong> {{ student.group }}
        </div>
        <div v-if="student.isDonePr != null" class="info-item">
          <strong>Здав ПР:</strong> {{ student.isDonePr }}
        </div>
        <div v-if="student.mark" class="info-item">
          <strong>Оцінка:</strong> {{ student.mark }}
        </div>
      </div>
    </div>
    <div v-if="error" class="error-message">Помилка завантаження даних.</div>
  </div>
</template>

<script>
import axios from "axios";
import { ref, onMounted, toRefs } from "vue";
import Modal from "./Modal.vue";

export default {
  components: { Modal },
  props: {
    id: String,
  },
  setup(props) {
    const isOpen = ref(false);
    const { id } = toRefs(props);
    const student = ref({});
    const loading = ref(true);
    const error = ref(false);

    onMounted(() => {
      axios
        .get(`http://34.82.81.113:3000/students/${id.value}`)
        .then(({ data }) => {
          student.value = data;
          loading.value = false;
        })
        .catch((err) => {
          console.error(err);
          error.value = true;
          loading.value = false;
        });
    });

    return { isOpen, student, loading, error };
  },
};
</script>

<style scoped>
.student-details {
  text-align: center;
  margin: 20px;
}

.loading-message,
.error-message {
  font-weight: bold;
  font-size: 16px;
  color: red;
}

.student-profile {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.student-photo {
  max-width: 200px;
  max-height: 200px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

.student-info {
  display: flex;
  flex-direction: column;
}

.info-item {
  margin: 10px 0;
  font-family: "Lucida Sans Unicode", "Lucida Grande", Sans-Serif;
}

.info-item strong {
  font-weight: bold;
}
</style>
