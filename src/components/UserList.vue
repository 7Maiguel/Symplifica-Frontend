<script>
import UserCard from "./UserCard.vue";

export default {
  data() {
    return {
      users: [],
      currentPage: 1,
      totalPages: 1,
      userToDelete: {},
      showVerifyModal: false
    };
  },
  components: {
    UserCard,
  },
  emits: ["back"],
  watch: {
    currentPage() {
      this.getUsers();
    },
  },
  methods: {
    async getUsers() {
      const res = await fetch(
        `http://localhost:3000/api/users?page=${this.currentPage}`
      );
      const resJSON = await res.json();

      if (resJSON.users.length == 0 && this.currentPage > 1) this.currentPage--;
      this.users = resJSON.users;
      this.totalPages = resJSON.total_pages;
    },
    async deleteUser(userId) {
      try {
        const res = await fetch(`http://localhost:3000/api/users/${userId}`, {
          method: "DELETE",
        });

        if (!res.ok) {
          throw {
            msg: "El usuario no pudo ser eliminado...",
            status: res.status,
          };
        }

        this.userToDelete = {};
        this.showVerifyModal = false;
        this.getUsers();
      } catch (e) {
        console.error(e.msg, e.status);
      }
    },
    openVerifyModal(user) {
      this.userToDelete = user;
      this.showVerifyModal = true;
    }
  },
  mounted() {
    this.getUsers();
  },
};
</script>

<template>
  <div
    class="w-[77vw] h-[84vh] bg-white rounded px-6 pt-10 pb-5 flex flex-col justify-between relative"
  >
    <img
      src="../assets/icons/left_arrow.svg"
      alt="left_arrow_icon"
      class="absolute top-9 cursor-pointer"
      @click="$emit('back')"
    />
    <h2 class="text-center text-3xl">Usuario registrados</h2>
    <div class="h-[88%] mt-6 grid grid-rows-2 grid-cols-4 gap-5" v-show="users.length >= 1">
      <UserCard
        v-for="user in users"
        :key="user.id"
        :userInfo="user"
        @delete-user="(user) => openVerifyModal(user)"
      />
    </div>
    <div class="h-[88%] flex items-center justify-center text-cyan-700 opacity-60" v-show="users.length == 0">
      No existen usuarios aún...
    </div>
    <div class="w-full flex justify-end mt-4" v-show="users.length >= 1">
      <div
        class="flex items-center"
        :class="
          currentPage == 1
            ? 'justify-end'
            : currentPage == totalPages
            ? 'mr-11'
            : 'justify-between'
        "
      >
        <img
          src="../assets/icons/chevron_left.svg"
          alt="previous_page_icon"
          class="cursor-pointer"
          v-show="currentPage > 1"
          @click="currentPage--"
        />
        <span
          class="text-xl font-semibold px-2 rounded-sm user-card"
          >{{ currentPage }}</span
        >
        <img
          src="../assets/icons/chevron_right.svg"
          alt="next_page_icon"
          class="cursor-pointer"
          v-show="currentPage < totalPages"
          @click="currentPage++"
        />
      </div>
    </div>
  </div>
  <div class="w-full h-full absolute top-0 left-0 bg-[rgba(0,0,0,0.35)] flex justify-center items-center" v-if="showVerifyModal">
    <div class="bg-white w-[30%] p-6 py-8 user-card rounded flex flex-col justify-center items-center">
      <p class="text-lg text-center mb-8 text-cyan-900">
        ¿Estas seguro de eliminar el usuario: <span class="font-semibold">{{ userToDelete.name + " " + userToDelete.last_name }}</span>?
      </p>
      <div class="w-[90%] flex justify-between">
        <button class="w-40 user-card bg-gray-50 text-gray-700 hover:bg-gray-100" @click="showVerifyModal = false">Cancelar</button>
        <button class="w-40" @click="deleteUser(userToDelete.id)">Si, eliminar</button>
      </div>
    </div>
  </div>
</template>
