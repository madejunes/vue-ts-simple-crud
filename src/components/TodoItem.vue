<template>
  <div class="flex items-center justify-between">
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
    <p
      class="i-heroicons-trash text-black cursor-pointer hover:text-red"
      @click="() => $emit('deleteItem', todo.id)"
    ></p>
  </div>
</template>

<script setup lang="ts">
import type { TTodo } from "../types";

defineProps({
  todo: {
    type: Object as TTodo,
    default: () => ({}),
  },
});

defineEmits(["deleteItem"]);
</script>
