<template>
  <div class="flex items-center justify-between">
    <div class="flex items-center">
      <input
        :id="todo.id.toString()"
        :name="todo.id.toString()"
        type="checkbox"
        :checked="todo.completed"
        class="h-4 w-4 text-teal-600 focus:ring-teal-500 border-gray-300 rounded"
        @change="(e) => onChange(e, todo.id)"
      />
      <label
        :for="todo.id.toString()"
        class="ml-3 block text-gray-900 text-left"
      >
        <span class="text-lg font-medium">{{ todo.title }}</span>
      </label>
    </div>
    <p
      class="i-heroicons-trash text-black cursor-pointer hover:text-red flex-shrink-0"
      @click="() => $emit('deleteItem', todo.id)"
    ></p>
  </div>
</template>

<script setup lang="ts">
import { ofetch } from "ofetch";
import { TTodo } from "../types";
import { apiUrl, userId } from "../contants";
import { PropType } from "vue";

// props
defineProps({
  todo: {
    type: Object as PropType<TTodo>,
    default: () => ({}),
  },
});

// emits
defineEmits(["deleteItem"]);

// methods
const onChange = async (e: any, id: number) => {
  await ofetch(`${apiUrl}/${id}`, {
    method: "PUT",
    body: {
      id,
      userId,
      completed: e.target.checked,
    },
  });
};
</script>
