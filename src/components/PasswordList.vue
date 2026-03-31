<script setup>
import PasswordItem from "./PasswordItem.vue";

defineProps({
  passwords: Array,
  user: Object,
});

const emit = defineEmits(["delete", "duplicate", "edit"]);

function forwardDelete(id) {
  emit("delete", id);
}
function forwardDuplicate(id) {
  emit("duplicate", id);
}
function forwardEdit(id) {
  emit("edit", id);
}
</script>

<template>
  <table>
    <thead>
      <tr>
        <th>Site</th>
        <th>Username</th>
        <th>Owner</th>
        <th>Criado</th>
        <th>Atualizado</th>
        <th>Senha</th>
        <th class="acao">Ações</th>
      </tr>
    </thead>
    <tbody>
      <PasswordItem
        v-for="item in passwords"
        :key="item.id"
        :item="item"
        :user="user"
        @delete="forwardDelete"
        @duplicate="forwardDuplicate"
        @edit="forwardEdit"
      />
      <tr v-if="passwords.length === 0">
        <td colspan="7">Nenhuma credencial encontrada.</td>
      </tr>
    </tbody>
  </table>
</template>

<style scoped>
table {
  width: 100%;
  border-collapse: collapse;
  border: 2px solid #00060f;
  margin-top: 20px;
}

th,
td {
  border: 2px solid #00060f;
  padding: 8px;
  background-color: #f4f4f5;
  text-align: center;
}

th {
  background-color: #053a9c;
  color: #fcfcfc;
}
</style>
