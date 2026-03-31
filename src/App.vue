<script setup>
import { ref, onMounted, watch } from "vue";
import PasswordList from "./components/PasswordList.vue";
import CredencialForm from "./components/CredencialForm.vue";
import { usePermissions } from "./composables/usePermissions";

const { setRole, canActions } = usePermissions();

const passwords = ref([]);
const users = ref([]);
const currentUser = ref(null);
const loading = ref(true);
const error = ref(null);

const showForm = ref(false);
const editingItem = ref(null);

onMounted(async () => {
  try {
    const [passwordRes, userRes] = await Promise.all([
      fetch("/data/passwords.json"),
      fetch("/data/users.json"),
    ]);

    if (!passwordRes.ok || !userRes.ok) {
      throw new Error("Falha ao carregar os dados");
    }

    passwords.value = await passwordRes.json();
    users.value = await userRes.json();

    const savedUserEmail = localStorage.getItem("activeUserEmail");
    const loggedUser = users.value.find((u) => u.email === savedUserEmail) || users.value.find((u) => u.email === "gustavo@example.com");

    currentUser.value = loggedUser || null;

    if (currentUser.value) {
      setRole(currentUser.value.role);
    }
  } catch (err) {
    error.value = err.message;
  } finally {
    loading.value = false;
  }
});

watch(currentUser, (newUser) => {
  if (newUser) {
    setRole(newUser.role);
    localStorage.setItem("activeUserEmail", newUser.email);
  }
});

function openCreateForm() {
  editingItem.value = null;
  showForm.value = true;
}

function openEditForm(id) {
  const item = passwords.value.find(p => p.id === id);
  if (item) {
    editingItem.value = { ...item };
    showForm.value = true;
  }
}

function closeForm() {
  showForm.value = false;
  editingItem.value = null;
}

function saveCredential(data) {
  if (editingItem.value) {
    const index = passwords.value.findIndex(p => p.id === editingItem.value.id);
    if (index !== -1) {
      passwords.value[index] = { 
        ...passwords.value[index], 
        ...data, 
        last_update: new Date().toISOString() 
      };
    }
  } else {
    passwords.value.push({
      id: "pwd-" + Date.now(),
      ...data,
      date_created: new Date().toISOString(),
      last_update: new Date().toISOString(),
      owner: currentUser.value.name
    });
  }
  closeForm();
}

function handleDelete(id) {
  passwords.value = passwords.value.filter(p => p.id !== id);
}

function handleDuplicate(id) {
  const item = passwords.value.find(p => p.id === id);
  if (item) {
    passwords.value.push({
      ...item,
      id: "pwd-" + Date.now(),
      date_created: new Date().toISOString(),
      last_update: new Date().toISOString()
    });
  }
}
</script>

<template>
  <div class="container">
    <h1>Gerenciador de Credenciais</h1>
    
    <div v-if="users.length > 0" style="margin-bottom: 20px;">
      <label>Usuário ativo: </label>
      <select v-model="currentUser">
        <option v-for="u in users" :key="u.id" :value="u">{{ u.name }} ({{ u.role }})</option>
      </select>
    </div>

    <div v-if="loading">Carregando...</div>
    <div v-else-if="error">{{ error }}</div>

    <div v-else style="width: 100%;">
      <button v-if="canActions" @click="openCreateForm" class="primary">Criar nova credencial</button>

      <PasswordList 
        :passwords="passwords" 
        :user="currentUser" 
        @delete="handleDelete"
        @duplicate="handleDuplicate"
        @edit="openEditForm"
      />
    </div>

    <CredencialForm 
      v-if="showForm" 
      :initialData="editingItem" 
      @save="saveCredential" 
      @cancel="closeForm" 
    />
  </div>
</template>

<style scoped>
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 20px 40px;
  font-family: sans-serif;
  color: #00060f;
  background-color: #fcfcfc;
  min-height: 100vh;
}

select {
  padding: 5px;
  border: 2px solid #00060f;
}

button {
  padding: 5px 10px;
  cursor: pointer;
  margin: 5px;
  border: 2px solid #00060f;
  background-color: #f4f4f5;
  color: #00060f;
}

button.primary {
  background-color: #053a9c;
  color: #fcfcfc;
  border-color: #053a9c;
}
</style>
