<script>
export default {
  props: ["userInfo"],
  emits: ["deleteUser"],
  computed: {
    fullName() {
      return `${this.userInfo.name} ${this.userInfo.last_name}`;
    },
    formattedGender() {
      return this.userInfo.gender == "male"
        ? "Masculino"
        : this.userInfo.gender == "female"
        ? "Femenino"
        : "Otro";
    },
  },
};
</script>

<template>
  <div
    class="w-full h-full flex flex-col items-center justify-between relative pt-10"
  >
    <img
      src="../assets/icons/user_circle.svg"
      alt="user_icon"
      class="absolute top-0"
    />
    <img src="../assets/icons/trash.svg" alt="trash_icon" class="absolute right-2 top-12 cursor-pointer" @click="$emit('deleteUser', userInfo)">
    <div
      class="w-full h-full flex flex-col justify-evenly bg-gray-100 rounded p-5 user-card"
    >
      <span :title="fullName">{{ fullName }}</span>
      <span :title="userInfo.email">{{ userInfo.email }}</span>
      <span :title="userInfo.phone">{{ userInfo.phone }}</span>
      <span :title="formattedGender">{{ formattedGender }}</span>
      <span :title="userInfo.address || 'Dirección desconocida'">{{
        userInfo.address || "Dirección desconocida"
      }}</span>
    </div>
  </div>
</template>
