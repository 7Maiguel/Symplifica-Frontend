<script>
import checkSound from "../assets/check.mp3";
import Input from "./shared/Input.vue";

export default {
  data() {
    return {
      user: {
        name: "",
        last_name: "",
        email: "",
        phone: "",
        gender: "",
        address: "",
      },
      errors: [],
      showSuccessMsg: false,
    };
  },
  components: {
    "v-input": Input,
  },
  emits: ["back"],
  methods: {
    typing(value, field) {
      this.user[field] = value;
      this.deleteInputError(field);
    },
    validateForm() {
      const emailRegExp = /^[\w-\.]+@([\w-]+\.)+[\w-]{2,4}$/g;

      if (!this.user.name ) this.errors[0] = "name";
      if (!this.user.last_name) this.errors[1] = "last_name";
      if (!this.user.email || !emailRegExp.test(this.user.email)) this.errors[2] = "email";
      if (!this.user.phone) this.errors[3] = "phone";
      if (!this.user.gender) this.errors[4] = "gender";

      this.errors = this.errors.filter(e => e != undefined);

      if (this.errors.length == 0) this.createUser();
    },
    deleteInputError(field) {
      const fieldIndex = this.errors.indexOf(field)
      if (fieldIndex >= 0) this.errors.splice(fieldIndex, 1);
    },
    async createUser() {
      try {
        const res = await fetch("http://localhost:3000/api/users", {
          method: "POST",
          body: JSON.stringify({ user: this.user }),
          headers: { "content-type": "application/json" },
        });

        if (!res.ok) {
          throw {
            msg: "El usuario no pudo ser creado...",
            status: res.status,
          };
        }

        this.showSuccessMsg = true;
        new Audio(checkSound).play();

      } catch (e) {
        console.error(e.msg, e.status);
      }
    }
  },
};
</script>

<template>
  <form
    class="w-[83%] h-full flex flex-col items-center my-5 justify-around relative"
    v-if="!showSuccessMsg"
  >
    <img
      src="../assets/icons/left_arrow.svg"
      alt="left_arrow"
      class="absolute top-4 left-[-5px] cursor-pointer"
      @click="$emit('back')"
    />

    <h2 class="text-2xl">Nuevo usuario</h2>

    <div class="w-full flex justify-between items-center">
      <label for="name" class="w-[22%]">
        Nombre<span class="text-cyan-500">*</span>
      </label>
      <v-input
        type="text"
        name="name"
        placeholder="Nombre"
        :model-value="user.name"
        :isInvalid="errors.includes('name')"
        @update:model-value="newValue => typing(newValue, 'name')"
      />
    </div>

    <div class="w-full flex justify-between items-center">
      <label for="lastname" class="w-[22%]">
        Apellido<span class="text-cyan-500">*</span>
      </label>
      <v-input
        type="text"
        name="lastname"
        placeholder="Apellido"
        :model-value="user.last_name"
        :isInvalid="errors.includes('last_name')"
        @update:model-value="newValue => typing(newValue, 'last_name')"
      />
    </div>

    <div class="w-full flex justify-between items-center">
      <label for="email" class="w-[22%]">
        Correo Electrónico<span class="text-cyan-500">*</span>
      </label>
      <v-input
        type="email"
        name="email"
        placeholder="Correo electrónico"
        :model-value="user.email"
        :isInvalid="errors.includes('email')"
        @update:model-value="newValue => typing(newValue, 'email')"
      />
    </div>

    <div class="w-full flex justify-between items-center">
      <label for="phone" class="w-[22%]">
        Celular<span class="text-cyan-500">*</span>
      </label>
      <v-input
        type="tel"
        name="phone"
        placeholder="Celular"
        :model-value="user.phone"
        :isInvalid="errors.includes('phone')"
        @update:model-value="newValue => typing(newValue, 'phone')"
      />
    </div>

    <div class="w-full flex justify-between items-center">
      <label for="gender">Género<span class="text-cyan-500">*</span></label>
      <v-input
        type="select"
        name="gender"
        :model-value="user.gender"
        :isInvalid="errors.includes('gender')"
        @update:model-value="newValue => typing(newValue, 'gender')"
      />
    </div>

    <div class="w-full flex justify-between items-center">
      <label for="address">Dirección</label>
      <input
        type="text"
        name="address"
        placeholder="Dirección (opcional)"
        class="w-[70%]"
        v-model="user.address"
      />
    </div>

    <div class="flex flex-col items-center">
      <button class="w-60 mb-1" @click.prevent="validateForm">
        Crear usuario
      </button>
      <span class="text-xs" :class="errors.length > 0 ? 'text-red-400 font-semibold' : 'text-gray-500'">Campos obligatorios (*)</span>
    </div>
  </form>

  <div class="flex flex-col items-center" v-else>
    <img src="../assets/icons/check_circle.svg" alt="check_icon" class="mb-2" />
    <h2 class="text-3xl mb-6">¡Usuario creado con éxito!</h2>
    <button class="w-60" @click="$emit('back')">Continuar</button>
  </div>
</template>
