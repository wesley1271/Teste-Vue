<script setup>
import { ref } from "vue";
const emits = defineEmits(["confirm", "cancel"]);

const code = ref("");
const error = ref(false);

function confirm() {
  if (code.value === "123456") {
    emits("confirm");
  } else {
    error.value = true;
  }
}
</script>

<template>
  <div class="modal">
    <div class="modal-content">
      <h2>Verificação MFA</h2>
      <p>Digite o código de autenticação para visualizar a senha:</p>
      <input v-model="code" type="text" placeholder="000000" />
      <p v-if="error" class="error">Código inválido. Tente novamente.</p>

      <div class="actions">
        <button class="primary" @click="confirm">Confirmar</button>
        <button @click="$emit('cancel')">Cancelar</button>
      </div>
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
  margin: 0;
}

input {
  padding: 8px;
  border: 2px solid #00060f;
  font-size: 1.2rem;
  letter-spacing: 4px;
  text-align: center;
}

.error {
  color: red;
  margin: 0;
  font-size: 0.85rem;
}

.actions {
  display: flex;
  gap: 8px;
  justify-content: flex-end;
}

button {
  padding: 6px 16px;
  border: 2px solid #00060f;
  background: #f4f4f5;
  cursor: pointer;
}

button.primary {
  background: #053a9c;
  color: #fcfcfc;
  border-color: #053a9c;
}
</style>
