<script setup>
import { ref, watch, computed } from "vue";

const props = defineProps({
  initialData: Object,
});

const emit = defineEmits(["save", "cancel"]);

const form = ref({
  site: "",
  username: "",
  password: "",
});

const isEdit = computed(() => !!props.initialData);

watch(
  () => props.initialData,
  (newVal) => {
    if (newVal) {
      form.value = { ...newVal };
    } else {
      form.value = { site: "", username: "", password: "" };
    }
  },
  { immediate: true }
);

function save() {
  if (!form.value.site || !form.value.username || !form.value.password) return;
  emit("save", { ...form.value });
}
</script>

<template>
  <div class="modal">
    <div class="modal-content">
      <h2>{{ isEdit ? 'Editar Credencial' : 'Nova Credencial' }}</h2>
      
      <form @submit.prevent="save">
        <div>
          <label>Site</label><br>
          <input v-model="form.site" type="text" required />
        </div>

        <div>
          <label>Username</label><br>
          <input v-model="form.username" type="text" required />
        </div>

        <div>
          <label>Password</label><br>
          <input v-model="form.password" type="text" required />
        </div>

        <div style="margin-top: 15px;">
          <button type="submit" class="primary">Salvar</button>
          <button type="button" @click="$emit('cancel')">Cancelar</button>
        </div>
      </form>
    </div>
  </div>
</template>

<style scoped>
.modal {
  position: fixed;
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
  background-color: rgba(244, 244, 245, 0.8);
  inset: 0;
}

.modal-content {
  background: #fcfcfc;
  padding: 32px;
  border: 2px solid #00060f;
  display: flex;
  flex-direction: column;
  gap: 12px;
  min-width: 300px;
}

h2 {
  color: #00060f;
  margin: 0 0 10px 0;
}

div {
  margin-bottom: 10px;
}

input {
  padding: 8px;
  border: 2px solid #00060f;
  width: 100%;
  box-sizing: border-box;
}

button {
  padding: 6px 16px;
  border: 2px solid #00060f;
  background: #f4f4f5;
  cursor: pointer;
  margin-right: 5px;
}

button.primary {
  background: #053a9c;
  color: #fcfcfc;
  border-color: #053a9c;
}
</style>
