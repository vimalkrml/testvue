<template>
  <div>
    <nav class="flex items-center justify-between bg-gray-800 p-4 h-14">
      <div class="flex items-center">
        <button
          @click="toggleMenu"
          v-if="!isOpen"
          class="text-white text-3xl mr-6"
        >
          ☰
        </button>
      </div>

      <div class="flex items-center">
        <a href="#" class="text-white mr-6">Tasks</a>
        <a href="#" class="text-white mr-6">Users</a>
        <a href="#" class="text-white">Help</a>
        <div class="relative ml-6">
          <img
            src="https://via.placeholder.com/40"
            alt="User Avatar"
            class="w-8 h-8 rounded-full border-2 border-white cursor-pointer"
            @click="toggleDropdown"
          />
          <span
            class="absolute bottom-0 right-0 w-3 h-3 bg-green-500 border-2 border-white rounded-full"
          ></span>

          <div
            v-if="dropdownOpen"
            class="absolute right-0 mt-3 w-48 bg-white text-gray-800 rounded-lg shadow-lg z-10 border"
          >
            <ul>
              <li>
                <a href="#" class="block px-4 py-2 hover:bg-gray-200"
                  >Profile</a
                >
              </li>
              <li>
                <a href="#" class="block px-4 py-2 hover:bg-gray-200"
                  >Settings</a
                >
              </li>
              <li>
                <a href="#" class="block px-4 py-2 hover:bg-gray-200">Logout</a>
              </li>
            </ul>
          </div>
        </div>
      </div>
    </nav>

    <div
      :class="[
        'fixed top-0 h-full bg-gray-800 text-white p-6 transition-all duration-300',
        isOpen ? 'w-44 lg:w-52 left-0' : 'w-0 -left-44',
      ]"
    >
      <button
        @click="toggleMenu"
        class="close-icon absolute top-4 right-4 text-2xl"
      >
        ×
      </button>
      <ul v-if="isOpen" class="mt-12">
        <li class="mb-4">
          <a href="#" class="text-white">Meetings</a>
        </li>
        <li class="mb-4">
          <div
            @click="toggleSubMenu"
            class="flex items-center justify-between cursor-pointer"
          >
            <a href="#" class="text-white">Services</a>
            <svg
              :class="[
                'w-4 h-4 transform transition-transform',
                isSubMenuOpen ? 'rotate-90' : 'rotate-0',
              ]"
              fill="none"
              stroke="currentColor"
              viewBox="0 0 24 24"
              xmlns="http://www.w3.org/2000/svg"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                stroke-width="2"
                d="M9 5l7 7-7 7"
              ></path>
            </svg>
          </div>
          <ul v-if="isSubMenuOpen" class="ml-4 mt-2">
            <li class="mb-2">
              <a href="#" class="text-gray-300">Poc</a>
            </li>
            <li>
              <a href="#" class="text-gray-300">Tickets</a>
            </li>
          </ul>
        </li>
        <li class="mb-4">
          <a href="#" class="text-white">Reports</a>
        </li>
      </ul>
    </div>

    <div
      :class="[
        'transition-all duration-300 p-6',
        isOpen ? 'ml-44 lg:ml-52' : 'ml-0',
      ]"
    >
      <TileView />
    </div>
  </div>
</template>

<script>
import TileView from "./TileView.vue";

export default {
  components: {
    TileView,
  },
  data() {
    return {
      isOpen: false,
      isSubMenuOpen: false,
      dropdownOpen: false,
    };
  },
  methods: {
    toggleMenu() {
      this.isOpen = !this.isOpen;
    },
    toggleSubMenu() {
      this.isSubMenuOpen = !this.isSubMenuOpen;
    },
    toggleDropdown() {
      this.dropdownOpen = !this.dropdownOpen;
    },
  },
};
</script>

<style scoped>
.close-icon {
  @apply cursor-pointer;
}
</style>
