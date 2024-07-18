<template>
  <div
    class="max-w-md mx-auto bg-white shadow-lg rounded-lg overflow-hidden mt-16 text-black"
  >
    <div class="px-4 py-2">
      <h1 class="text-gray-800 font-bold text-2xl uppercase">To-Do List</h1>
    </div>
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

    <p v-if="isQueryFetching">Loading your list ...</p>
    <div v-if="isQueryError">
      <p>
        Error loading your list.
        <span class="underline cursor-pointer" @click="onTryAgain"
          >Please try again.</span
        >
      </p>
      <p class="text-slate-4">{{ error }}</p>
    </div>
    <ul class="px-4 list-none" v-if="todos?.length">
      <li class="pb-4" v-for="todo_ in todos_" :key="todo_.id">
        <TodoItem :todo="todo_" @delete-item="(e) => onDeleteTodo(e)" />
      </li>
      <li class="pb-4" v-for="todo in todos" :key="todo.id">
        <TodoItem :todo="todo" @delete-item="(e) => onDeleteTodo(e)" />
      </li>
    </ul>
  </div>
</template>

<script setup lang="ts">
import TodoItem from "./TodoItem.vue";
import { ref } from "vue";
import { useMutation, useQuery } from "@tanstack/vue-query";
import { ofetch } from "ofetch";
import type { TTodo } from "../types";

// constants
const userId = 9;
const apiUrl = "https://jsonplaceholder.typicode.com/todos";

// local state
const newTodo = ref("");
const todos_ = ref<Array<TTodo>>([]);

// vue query hooks
const {
  isFetching: isQueryFetching,
  isError: isQueryError,
  data: todos,
  error,
} = useQuery({
  queryKey: ["todos"],
  queryFn: async () => {
    const resp: Array<TTodo> = await ofetch(`${apiUrl}?userId=${userId}`);
    return resp;
  },
});
const mutation = useMutation({
  mutationFn: async (body: { id: number; title: string; userId: number }) => {
    await ofetch(apiUrl, {
      method: "POST",
      body: body,
    });
  },
});

// methods
const onTryAgain = () => {
  window.location.reload();
};
const onAddNewTodo = () => {
  if (newTodo.value) {
    const dataToAdd = {
      id: Date.now(),
      title: newTodo.value,
      userId,
      completed: false,
    };
    mutation.mutate(dataToAdd);
    newTodo.value = "";
    todos_.value?.push(dataToAdd);
  }
};
const onDeleteTodo = async (id: number) => {
  const indexToDel = todos_.value?.findIndex((t) => (t.id = id));
  if (indexToDel > -1) {
    todos_.value?.splice(indexToDel, 1);
  }

  await ofetch(`${apiUrl}/${id}`, { method: "DELETE" });
};
</script>
