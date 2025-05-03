<script setup>
import { ref, watch, onMounted, computed } from 'vue';

// Récupération des tâches depuis localStorage
const todos = ref([]);
const newTodo = ref('');
const errorMessage = ref('');
const filter = ref('all');
const darkMode = ref(false);

onMounted(() => {
  const savedTodos = localStorage.getItem('todos');
  if (savedTodos) {
    todos.value = JSON.parse(savedTodos);
  }

  const savedDarkMode = localStorage.getItem('darkMode');
  darkMode.value = savedDarkMode === 'true';
});

// Sauvegarde dans localStorage
watch(todos, (newVal) => {
  localStorage.setItem('todos', JSON.stringify(newVal));
}, { deep: true });

watch(darkMode, (newVal) => {
  localStorage.setItem('darkMode', newVal);
});

// Ajout d'une nouvelle tâche
const addTodo = () => {
  if (newTodo.value.trim() === '') {
    errorMessage.value = '⚠️ La tâche ne peut pas être vide.';
    return;
  }

  todos.value.push({ text: newTodo.value, done: false });
  newTodo.value = '';
  errorMessage.value = '';
};

// Basculer entre terminé / non terminé
const toggleTodo = (index) => {
  todos.value[index].done = !todos.value[index].done;
};

// Supprimer une tâche
const removeTodo = (index) => {
  todos.value.splice(index, 1);
};

// Filtrer les tâches
const filteredTodos = computed(() => {
  if (filter.value === 'done') return todos.value.filter(todo => todo.done);
  if (filter.value === 'pending') return todos.value.filter(todo => !todo.done);
  return todos.value;
});

// Compteur de tâches restantes
const remainingTasks = computed(() => {
  return todos.value.filter(todo => !todo.done).length;
});

// Basculer le mode sombre
const toggleDarkMode = () => {
  darkMode.value = !darkMode.value;
};
</script>

<template>
  <div :class="['container', { dark: darkMode }]">
    <h1>Ma Todo List ✍️</h1>

    <!-- Bouton Mode Sombre -->
    <button @click="toggleDarkMode" class="dark-mode-btn">
      {{ darkMode ? '☀️ Mode Clair' : '🌙 Mode Sombre' }}
    </button>

    <!-- Saisie de la tâche -->
    <div class="input-section">
      <input v-model="newTodo" @keyup.enter="addTodo" placeholder="Ajouter une tâche..." />
      <button @click="addTodo">Ajouter</button>
    </div>

    <p v-if="errorMessage" class="error">{{ errorMessage }}</p>

    <!-- Filtrage -->
    <div class="filters">
      <button @click="filter = 'all'" :class="{ active: filter === 'all' }">Toutes</button>
      <button @click="filter = 'pending'" :class="{ active: filter === 'pending' }">À faire</button>
      <button @click="filter = 'done'" :class="{ active: filter === 'done' }">Terminées</button>
    </div>

    <!-- Compteur -->
    <p class="counter">Tâches restantes : <strong>{{ remainingTasks }}</strong></p>

    <!-- Liste des tâches -->
    <ul>
      <transition-group name="fade">
        <li v-for="(todo, index) in filteredTodos" :key="index" :class="{ done: todo.done }">
          <span @click="toggleTodo(index)">{{ todo.text }}</span>
          <button @click="removeTodo(index)">❌</button>
        </li>
      </transition-group>
    </ul>
  </div>
</template>

<style scoped>
/* Style de base */
.container {
  max-width: 400px;
  margin: auto;
  text-align: center;
  font-family: Arial, sans-serif;
  background: #fff;
  color: #333;
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.1);
  transition: 0.3s;
}
.dark {
  background: #222;
  color: #eee;
  box-shadow: 0px 0px 10px rgba(255, 255, 255, 0.1);
}
.input-section {
  display: flex;
  gap: 10px;
  margin-bottom: 10px;
}
input {
  flex: 1;
  padding: 8px;
  font-size: 16px;
  border: 2px solid #42b983;
  border-radius: 5px;
  outline: none;
}
button {
  padding: 8px 12px;
  background-color: #42b983;
  color: white;
  border: none;
  cursor: pointer;
  border-radius: 5px;
  transition: 0.2s;
}
button:hover {
  background-color: #36956a;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: flex;
  justify-content: space-between;
  padding: 10px;
  background: #f4f4f4;
  margin-bottom: 5px;
  border-radius: 5px;
  cursor: pointer;
  transition: 0.2s;
}
li:hover {
  background-color: #e0e0e0;
}
.done {
  text-decoration: line-through;
  color: gray;
}
.error {
  color: red;
  font-size: 14px;
  margin-bottom: 10px;
}
.fade-enter-active, .fade-leave-active {
  transition: all 0.3s ease;
}
.fade-enter, .fade-leave-to {
  opacity: 0;
  transform: translateY(-10px);
}
.filters {
  margin: 10px 0;
}
.filters button {
  background: none;
  border: 1px solid #42b983;
  color: #42b983;
  padding: 5px 10px;
  margin: 2px;
  cursor: pointer;
  border-radius: 5px;
  transition: 0.2s;
}
.filters button:hover, .filters .active {
  background-color: #42b983;
  color: white;
}
.counter {
  font-size: 14px;
  margin-top: 10px;
}
.dark-mode-btn {
  background: none;
  border: 1px solid #42b983;
  color: #42b983;
  padding: 5px 10px;
  margin-bottom: 10px;
  cursor: pointer;
  border-radius: 5px;
  transition: 0.2s;
}
.dark-mode-btn:hover {
  background-color: #42b983;
  color: white;
}
</style>



