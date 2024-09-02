<template>
  <div>
    <!-- Search Bar -->
    <div class="p-2 mb-8">
      <input
        v-model="searchQuery"
        type="text"
        placeholder="Search students..."
        class="w-full p-2 border border-gray-300 rounded-lg"
      />
    </div>

    <!-- Skeleton Loader for Search Results -->
    <div
      v-if="isLoadingSearch"
      class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4 mt-4"
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

    <!-- Draggable Tiles -->
    <draggable
      v-if="!isLoadingSearch && !isLoading"
      v-model="filteredStudents"
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

    <!-- No Data Message -->
    <div
      v-if="!filteredStudents.length && !isLoading && !isLoadingSearch"
      class="p-4 text-center text-gray-500"
    >
      No data available
    </div>

    <!-- Loading Skeleton for Initial Data -->
    <div
      v-if="isLoading && !isLoadingSearch"
      class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4 mt-4"
    >
      <div
        v-for="n in 10"
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

    <!-- Modal -->
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
      searchQuery: "",
      isLoadingSearch: false,
    };
  },
  computed: {
    filteredStudents() {
      const query = this.searchQuery.toLowerCase();
      return this.students.filter(
        (student) =>
          student.name.toLowerCase().includes(query) ||
          student.email.toLowerCase().includes(query)
      );
    },
    isSmallScreen() {
      return window.innerWidth < 640;
    },
  },
  watch: {
    searchQuery(newQuery) {
      if (newQuery.length > 0) {
        this.isLoadingSearch = true;
        setTimeout(() => {
          this.isLoadingSearch = false;
        }, 500); // Simulated delay for search
      } else {
        this.isLoadingSearch = false;
      }
    },
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
input:focus,
textarea:focus,
select:focus {
  outline: none;
  box-shadow: none;
  border-color: rgb(182, 182, 182);
}

input,
textarea,
select {
  border: 1px solid rgb(182, 182, 182);
}

input:active,
textarea:active,
select:active {
  outline: none;
  box-shadow: none;
}

.loader-bar {
  width: 100%;
  height: 6px;
  background: linear-gradient(
    90deg,
    rgba(53, 113, 224, 0.6) 100%,
    rgba(65, 105, 225, 0.6) 50%,
    rgba(1, 74, 209, 0.6) 15%
  );
  background-size: 200% 100%;
  border-radius: 50px;
  animation: loading 2s linear infinite;
}

@keyframes loading {
  0% {
    background-position: 0 0;
  }
  100% {
    background-position: 100% 0;
  }
}

.loader-container {
  display: flex;
  justify-content: center;
  align-items: center;
  margin-top: 2rem;
  height: 6px;
}
</style>
