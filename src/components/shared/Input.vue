<script>
export default {
  props: ["type", "name", "placeholder", "isInvalid", "modelValue"],
  emits: ["update:model-value"]
};
</script>

<template>
  <div class="w-[70%] flex flex-col relative">
    <input
      :type="type"
      :placeholder="placeholder"
      :value="modelValue"
      :class="{ 'input-error': isInvalid }"
      v-if="type != 'select'"
      @input="$emit('update:model-value', $event.target.value)"
    />
    <select
      :name="name"
      :value="modelValue"
      :class="{ 'input-error': isInvalid }"
      @change="$emit('update:model-value', $event.target.value)"
      v-else
    >
      <option value="" disabled selected>Género</option>
      <option value="male">Masculino</option>
      <option value="female">Femenino</option>
      <option value="other">Otro</option>
    </select>
    <span
      class="font-semibold text-xs text-red-400 absolute left-0 bottom-[-22px]"
      v-show="isInvalid"
    >
      {{
        type == "email"
          ? "Ingrese un correo electrónico valido"
          : "Este campo es obligatorio"
      }}
    </span>
  </div>
</template>
