<script>
import UserForm from "./components/UserForm.vue";
import UserList from "./components/UserList.vue";

export default {
  data() {
    return {
      usersLength: 0,
      loadingUserForm: false,
      showUserForm: false,
      showAllUsers: false,
    };
  },
  components: {
    UserForm,
    UserList,
  },
  methods: {
    renderHome() {
      this.showAllUsers = false;
      this.showUserForm = false;
      this.getUsersLength();
    },
    async getUsersLength() {
      try {
        const res = await fetch("http://localhost:3000/api/userslength");
        this.usersLength = await res.json();
      } catch (e) {
        console.error(e);
      }
    },
  },
  mounted() {
    this.getUsersLength();
  },
};
</script>

<template>
  <div
    class="w-[77vw] h-[85vh] bg-white rounded flex flex-row"
    v-if="!showAllUsers"
  >
    <img
      src="./assets/users_wallpaper.jpg"
      alt="users_wallpaper"
      class="w-[43%] h-full bg-black object-cover"
    />
    <div class="w-[57%] flex flex-col items-center justify-center">
      <div class="flex flex-col items-center w-full" v-if="!showUserForm">
        <img
          src="./assets/icons/user_circle.svg"
          alt="user_icon"
          class="mb-2"
        />
        <h1 class="text-4xl font-semibold mb-6 text-cyan-900">
          Registro de usuarios
        </h1>
        <div class="flex justify-center">
          <button class="mr-2" @click="showUserForm = true">
            Registrar nuevo usuario
          </button>
          <button @click="showAllUsers = true">
            Ver usuarios registrados ({{ usersLength }})
          </button>
        </div>
      </div>
      <UserForm @back="renderHome" v-else />
    </div>
  </div>

  <UserList @back="renderHome" v-else />
</template>
