<script setup>
import { usePermissions } from "@/composables/usePermissions";
import { ref } from "vue";
import MfaModal from "./MfaModal.vue";

defineProps(["item", "user"]);
const emit = defineEmits(["delete", "duplicate", "edit"]);

const { canActions, canView } = usePermissions();

const showMfa = ref(false);
const passwordVisible = ref(false);

function showPassword() {
  showMfa.value = false;
  passwordVisible.value = true;
}
</script>

<template>
  <tr>
    <td>{{ item.site }}</td>
    <td>{{ item.username }}</td>
    <td>{{ item.owner }}</td>
    <td>
      {{ item.date_created ? new Date(item.date_created).toLocaleDateString() : "-" }}
    </td>
    <td>
      {{ item.last_update ? new Date(item.last_update).toLocaleDateString() : "-" }}
    </td>
    <td>
      {{
        passwordVisible
          ? item.password
          : "•".repeat(Math.max(item.password?.length || 8, 8))
      }}
    </td>
    <td>
      <button v-if="canActions" @click="showMfa = true">Ver senha</button>
      <button v-if="canActions" @click="emit('edit', item.id)">Editar</button>
      <button v-if="canActions" @click="emit('duplicate', item.id)">Duplicar</button>
      <button v-if="canActions" @click="emit('delete', item.id)">Excluir</button>
    </td>
  </tr>

  <MfaModal v-if="showMfa" @confirm="showPassword" @cancel="showMfa = false" />
</template>

<style scoped>
button {
  padding: 5px 10px;
  background-color: #053a9c;
  color: white;
  cursor: pointer;
  margin: 2px;
  border: 2px solid #00060f;
}

th,
td {
  border: 1px solid #00060f;
  text-align: center;
}
</style>
