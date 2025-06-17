<script setup>
import { computed, onMounted, ref } from "vue";
import StatusFilter from "./components/StatusFilter.vue";
import TodoItem from "./components/TodoItem.vue";
import Message from "./components/Message.vue";
import * as todoApi from "./api/todos.js";

const todos = ref([]);
const title = ref("");
const status = ref("all");
const errorMessage = ref(null); // посилання на компонент Message
const messageText = ref(""); // текст повідомлення

const showMessage = (text) => {
  messageText.value = text;
  errorMessage.value?.show(text);
};

const hideMessage = () => {
  messageText.value = "";
  errorMessage.value?.hide();
};

onMounted(async () => {
  try {
    todos.value = await todoApi.getTodos();
  } catch (error) {
    showMessage("Unable to load todos");
  }

  if (!Array.isArray(todos.value)) {
    todos.value = [];
  }
});

const addTodo = async () => {
  if (!title.value) {
    showMessage("Title should not be empty");
    return;
  }

  try {
    const newTodo = await todoApi.createTodo(title.value);
    todos.value.push(newTodo);
    title.value = "";
    hideMessage();
  } catch (error) {
    showMessage("Unable to add a todo");
  }
};

const activeTodos = computed(() =>
  todos.value.filter((todo) => !todo.completed)
);

const visibleTodos = computed(() => {
  if (status.value === "active") {
    return todos.value.filter((todo) => !todo.completed);
  } else if (status.value === "completed") {
    return todos.value.filter((todo) => todo.completed);
  }
  return todos.value;
});

const deleteTodo = async (todoId) => {
  try {
    await todoApi.deleteTodo(todoId);
    todos.value = todos.value.filter((todo) => todoId !== todo.id);
  } catch (error) {
    showMessage("Unable to delete a todo");
  }
};

const updateTodo = async ({ id, title, completed }) => {
  try {
    const updatedTodo = await todoApi.updateTodo({ id, title, completed });
    const currentTodo = todos.value.find((todo) => todo.id === id);
    Object.assign(currentTodo, updatedTodo);
  } catch (error) {
    showMessage("Unable to update a todo");
  }
};
</script>

<template>
  <div class="todoapp">
    <h1 class="todoapp__title">todos {{ todos.length }}</h1>

    <div class="todoapp__content">
      <header class="todoapp__header">
        <button
          v-if="todos.length > 0"
          type="button"
          class="todoapp__toggle-all"
          :class="{ active: activeTodos.length === 0 }"
        ></button>

        <form @submit.prevent="addTodo">
          <input
            data-cy="NewTodoField"
            type="text"
            class="todoapp__new-todo"
            placeholder="What needs to be done?"
            v-model="title"
            @input="hideMessage"
          />
        </form>
      </header>

      <TransitionGroup
        tag="section"
        name="todolist"
        class="todoapp__main"
        v-if="todos.length > 0"
      >
        <TodoItem
          v-for="(todo, i) in visibleTodos"
          :key="todo.id"
          :todo="todo"
          @delete="deleteTodo(todo.id)"
          @update="updateTodo($event)"
        />
      </TransitionGroup>

      <footer
        v-if="todos.length > 0"
        class="todoapp__footer"
        data-cy="Footer"
      >
        <span class="todo-count" data-cy="TodosCounter">
          {{ activeTodos.length }}
        </span>

        <StatusFilter v-model="status" />

        <button
          type="button"
          class="todoapp__clear-completed"
          :disabled="todos.length === activeTodos.length"
          @click="todos = activeTodos"
        >
          Clear completed
        </button>
      </footer>
    </div>

    <!-- Повідомлення про помилку -->
    <Message ref="errorMessage" class="is-danger">
      <template #header="{ someValue }">
        <p>Server Error {{ someValue }}</p>
      </template>
      <template #default>
        <p>{{ messageText }}</p>
      </template>
    </Message>
  </div>
</template>

<style scoped>
.todolist-enter-active,
.todolist-leave-active {
  max-height: 60px;
  transition: all 0.5s ease;
}
.todolist-enter-from,
.todolist-leave-to {
  opacity: 0;
  max-height: 0;
  transform: scaleY(0);
}
</style>
