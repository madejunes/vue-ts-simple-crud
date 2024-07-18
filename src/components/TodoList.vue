<template>
  <div
    class="max-w-md mx-auto bg-white shadow-lg rounded-lg overflow-hidden mt-16 text-black"
  >
    <div class="px-4 py-2">
      <h1 class="text-gray-800 font-bold text-2xl uppercase">To-Do List</h1>
    </div>
    <!-- <p class="text-red" v-if="isTodoExist">
      "{{ newTodo }}" Already on your list
    </p> -->
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
      <li class="pb-4" v-for="todo in todos" :key="todo.id">
        <div class="flex items-center">
          <input
            :id="todo.id.toString()"
            :name="todo.id.toString()"
            type="checkbox"
            :checked="todo.completed"
            class="h-4 w-4 text-teal-600 focus:ring-teal-500 border-gray-300 rounded"
          />
          <label
            :for="todo.id.toString()"
            class="ml-3 block text-gray-900 text-left"
          >
            <span class="text-lg font-medium">{{ todo.title }}</span>
          </label>
        </div>
      </li>
    </ul>
  </div>
</template>

<script setup lang="ts">
import { ref } from "vue";
import { useMutation, useQuery, useQueryClient } from "@tanstack/vue-query";
import { ofetch } from "ofetch";

// types
type TTodo = {
  userId: number;
  id: number;
  title: string;
  completed: boolean;
};

// constants
const userId = 9;
const apiUrl = "https://jsonplaceholder.typicode.com/todos";

// local state
const newTodo = ref("");

// vue query hooks
const queryClient = useQueryClient();
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
  onSuccess: () => {
    queryClient.invalidateQueries({ queryKey: ["todos"] });
  },
});

// methods
const onTryAgain = () => {
  window.location.reload();
};
const onAddNewTodo = () => {
  if (newTodo.value) {
    mutation.mutate({
      id: Date.now(),
      title: newTodo.value,
      userId,
    });
    newTodo.value = "";
  }
};
</script>
