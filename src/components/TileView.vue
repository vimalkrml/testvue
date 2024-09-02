<template>
  <div>
    <draggable
      v-model="students"
      group="tiles"
      class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4"
      item-key="id"
    >
      <template #item="{ element }">
        <div
          @click="selectStudent(element)"
          class="tile p-4 bg-white border rounded-lg hover:bg-gray-100 cursor-pointer"
          :class="{ 'border-2 border-blue-500': selectedStudent === element }"
        >
          <div class="flex items-center justify-between">
            <h2
              class="text-lg font-semibold truncate"
              :class="{ 'text-base': isSmallScreen }"
            >
              {{ element.name }}
            </h2>
            <span
              class="drag-handle text-gray-400 cursor-move"
              :class="{ 'text-sm': isSmallScreen }"
            >
              &#9776;
            </span>
          </div>
          <p
            class="text-gray-700 truncate"
            :class="{ 'text-sm': isSmallScreen }"
          >
            {{ element.email }}
          </p>
          <div class="mt-2 flex space-x-2">
            <button
              @click.stop="editStudent(element)"
              class="px-2 py-1 text-xs font-semibold text-blue-500 bg-blue-100 rounded-full"
              :class="{ 'text-xxs': isSmallScreen }"
            >
              Edit
            </button>
            <button
              @click.stop="flagStudent(element)"
              class="px-2 py-1 text-xs font-semibold text-yellow-500 bg-yellow-100 rounded-full"
              :class="{ 'text-xxs': isSmallScreen }"
            >
              Flag
            </button>
            <button
              @click.stop="deleteStudent(element)"
              class="px-2 py-1 text-xs font-semibold text-red-500 bg-red-100 rounded-full"
              :class="{ 'text-xxs': isSmallScreen }"
            >
              Delete
            </button>
          </div>
        </div>
      </template>
    </draggable>

    <div
      v-if="isLoading"
      class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4 mt-6"
    >
      <div
        v-for="n in 12"
        :key="n"
        class="p-4 bg-gray-200 rounded-lg animate-pulse"
      >
        <div class="h-4 bg-gray-300 rounded w-3/4 mb-2"></div>
        <div class="h-4 bg-gray-300 rounded mb-2"></div>
        <div class="flex space-x-2">
          <div class="h-4 bg-gray-300 rounded w-1/4"></div>
          <div class="h-4 bg-gray-300 rounded w-1/4"></div>
          <div class="h-4 bg-gray-300 rounded w-1/4"></div>
        </div>
      </div>
    </div>

    <div
      v-if="selectedStudent && !isLoading"
      class="modal fixed inset-0 flex items-center justify-center bg-black bg-opacity-50 p-4"
    >
      <div class="bg-white p-6 rounded-lg shadow-xl w-full max-w-lg relative">
        <button
          @click="closeModal"
          class="absolute top-4 right-4 text-gray-600 hover:text-gray-900 text-2xl"
        >
          &times;
        </button>
        <h2 class="text-2xl font-bold mb-4">{{ selectedStudent.name }}</h2>
        <p class="text-gray-700 mb-2">
          <strong>Username:</strong> {{ selectedStudent.username }}
        </p>
        <p class="text-gray-700 mb-2">
          <strong>Email:</strong> {{ selectedStudent.email }}
        </p>
        <p class="text-gray-700 mb-2">
          <strong>Phone:</strong> {{ selectedStudent.phone }}
        </p>
        <p class="text-gray-700 mb-2">
          <strong>Website:</strong> {{ selectedStudent.website }}
        </p>
        <p class="text-gray-700 mb-2">
          <strong>Company:</strong> {{ selectedStudent.company.name }}
        </p>
        <p class="text-gray-700 mb-2">
          <strong>Catchphrase:</strong>
          {{ selectedStudent.company.catchPhrase }}
        </p>
        <p class="text-gray-700 mb-2">
          <strong>Address:</strong> {{ selectedStudent.address.street }},
          {{ selectedStudent.address.suite }},
          {{ selectedStudent.address.city }} -
          {{ selectedStudent.address.zipcode }}
        </p>
        <div class="mt-4 flex space-x-2">
          <span
            class="inline-block px-2 py-1 text-xs font-semibold text-white rounded-full"
            :class="getLatClass(selectedStudent.address.geo.lat)"
          >
            Lat: {{ selectedStudent.address.geo.lat }}
          </span>
          <span
            class="inline-block px-2 py-1 text-xs font-semibold text-white rounded-full"
            :class="getLngClass(selectedStudent.address.geo.lng)"
          >
            Lng: {{ selectedStudent.address.geo.lng }}
          </span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import draggable from "vuedraggable";

export default {
  components: {
    draggable,
  },
  data() {
    return {
      students: [],
      selectedStudent: null,
      isLoading: true,
    };
  },
  mounted() {
    setTimeout(() => {
      axios
        .get("https://jsonplaceholder.typicode.com/users")
        .then((response) => {
          this.students = response.data;
          this.isLoading = false;
        });
    }, 1000);
  },
  methods: {
    selectStudent(student) {
      this.selectedStudent = student;
    },
    closeModal() {
      this.selectedStudent = null;
    },
    editStudent(student) {
      alert(`Edit ${student.name}`);
    },
    flagStudent(student) {
      alert(`Flag ${student.name}`);
    },
    deleteStudent(student) {
      alert(`Delete ${student.name}`);
    },
    getLatClass(lat) {
      return lat < 0 ? "bg-red-500" : "bg-green-500";
    },
    getLngClass(lng) {
      return lng < 0 ? "bg-yellow-500" : "bg-blue-500";
    },
  },
};
</script>

<style scoped>
.modal {
  @apply z-50;
}
.tile {
  @apply cursor-pointer;
}
.drag-handle {
  @apply text-lg;
}
</style>
