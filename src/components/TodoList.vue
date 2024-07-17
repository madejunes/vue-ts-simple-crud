<template>
  <div
    class="max-w-md mx-auto bg-white shadow-lg rounded-lg overflow-hidden mt-16"
  >
    <div class="px-4 py-2">
      <h1 class="text-gray-800 font-bold text-2xl uppercase">To-Do List</h1>
    </div>
    <p class="text-red" v-if="isTodoExist">
      "{{ newTodo }}" Already on your list
    </p>
    <form class="px-4 py-2">
      <div class="flex items-center border-b-2 border-teal-500 py-2">
        <input
          class="appearance-none bg-transparent border-none w-full text-gray-700 mr-3 py-1 px-2 leading-tight focus:outline-none"
          type="text"
          placeholder="Add a task"
          v-model="newTodo"
        />
        <button
          class="flex-shrink-0 bg-teal-500 hover:bg-teal-700 border-teal-500 hover:border-teal-700 text-sm border-4 text-white py-1 px-2 rounded"
          type="button"
          @click="onAddNewTodo"
        >
          Add
        </button>
      </div>
    </form>

    <ul class="divide-y divide-gray-200 px-4">
      <li class="py-4" v-for="todo in todos" :key="todo.id">
        <div class="flex items-center">
          <input
            :id="todo.id.toString()"
            :name="todo.id.toString()"
            type="checkbox"
            class="h-4 w-4 text-teal-600 focus:ring-teal-500 border-gray-300 rounded"
          />
          <label :for="todo.id.toString()" class="ml-3 block text-gray-900">
            <span class="text-lg font-medium">{{ todo.title }}</span>
          </label>
        </div>
      </li>
    </ul>
  </div>
</template>

<script setup lang="ts">
import { ref, watch } from "vue";

// local state
const newTodo = ref("");
const todos = ref([
  { id: 1, title: "Finish project proposal lala" },
  { id: 2, title: "Buy groceries" },
  { id: 3, title: "Go for a run" },
]);
const isTodoExist = ref(false);

// methods
const checkIsTodoExist = () => {
  const existingTodos = todos.value.find(
    (t) => t.title.toLowerCase() === newTodo.value.toLowerCase()
  );
  if (existingTodos?.title) {
    isTodoExist.value = true;
    return true;
  } else {
    isTodoExist.value = false;
    return false;
  }
};
const onAddNewTodo = () => {
  if (newTodo.value && !checkIsTodoExist()) {
    todos.value.push({ id: Math.random(), title: newTodo.value });
  }
};

// watcher
watch(newTodo, (newVal) => {
  if (isTodoExist.value && newVal) {
    isTodoExist.value = false;
  }
});
</script>
