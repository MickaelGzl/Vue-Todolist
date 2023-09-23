<template>
  <h1 v-once>ToDoList</h1>
  <div v-memo="[tache]">
    <input v-model="tache" type="text" @keyup.enter="addToTodos" />
    <button type="button" @click="addToTodos">Ajouter</button>
  </div>
  <ul v-if="todos.length">
    <li v-for="{ content, _id, onEdition } in todos" :key="_id">
      <template v-if="!onEdition">
        <span>{{ content }}</span>
        <button @click="editTodo(_id)">Edit</button>
        <button @click="deleteToTodo(_id)">Suppr</button>
      </template>
      <template v-else>
        <input
          v-model="tacheEdit[_id]"
          type="text"
          @keyup.enter="validateEditTodo(_id)"
        />
        <button @click="validateEditTodo(_id)">Confirm</button>
      </template>
    </li>
  </ul>
  <h3 v-else>Aucune tâche en cours</h3>
</template>

<script setup lang="ts">
import { reactive, ref } from "vue";

interface Todos {
  _id: string;
  content: string;
  onEdition: boolean;
}

const todos = reactive<Todos[]>([]);

function createUniqueId() {
  const newId = Math.random().toString();
  let idAlreadyExist = todos.some((todo) => todo._id === newId);

  if (idAlreadyExist) {
    return createUniqueId();
  }
  return newId;
}

function addToTodos() {
  if (!tache.value) {
    return;
  }
  let newId = createUniqueId();
  todos.push({
    _id: newId,
    content: tache.value,
    onEdition: false,
  });
  tache.value = "";
}

function deleteToTodo(_id: string) {
  const index = todos.findIndex((todo) => todo._id === _id);
  todos.splice(index, 1);
}

function editTodo(_id: string) {
  const index = todos.findIndex((todo) => todo._id === _id);
  tacheEdit[_id] = todos[index].content;
  todos[index].onEdition = true;
}

function validateEditTodo(_id: string) {
  const index = todos.findIndex((todo) => todo._id === _id);
  todos[index].content = tacheEdit[_id];
  todos[index].onEdition = false;
  delete tacheEdit[_id];
}

const tache = ref("");
const tacheEdit = reactive<{ [key: string]: string }>({});
</script>

<style scoped>
li {
  list-style: none;
}
</style>
