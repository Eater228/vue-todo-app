<script setup>
import todos from "./data/todos.js";
</script>

<template>
  <div class="todoapp">
    <h1 class="todoapp__title">todos {{ todos.length }}</h1>

    <div class="todoapp__content">
      <header class="todoapp__header">
        <!--this button should have `active` class only if all todos are completed -->
        <button
          type="button"
          class="todoapp__toggle-all active"
          data-cy="ToggleAllButton"
        ></button>

        <!-- Add a todo on form submit -->
        <form>
          <input
            data-cy="NewTodoField"
            type="text"
            class="todoapp__new-todo"
            placeholder="What needs to be done?"
          />
        </form>
      </header>

      <section class="todoapp__main" data-cy="TodoList">
        <!-- This is a completed todo -->
        <div
          v-for="todo of todos"
          :key="todo.id"
          class="todo"
          v-bind:class="{ completed: todo.completed }"
        >
          <label class="todo__status-label">
            <input
              type="checkbox"
              class="todo__status"
              :checked="todo.completed"
            />
          </label>

          <form v-if="false">
            <input
              type="text"
              class="todo__title-field"
              placeholder="Empty todo will be deleted"
            />
          </form>

          <template v-else>
            <span class="todo__title">{{ todo.title }}</span>
            <button class="todo__remove">×</button>
          </template>

          <div class="modal overlay" :class="{ 'is-active': false }">
            <div class="modal-background has-background-white-ter"></div>
            <div class="loader"></div>
          </div>
        </div>
      </section>

      <!-- Hide the footer if there are no todos -->
      <footer class="todoapp__footer" data-cy="Footer">
        <span class="todo-count" data-cy="TodosCounter"> 3 items left </span>

        <!-- Active link should have the 'selected' class -->
        <nav class="filter" data-cy="Filter">
          <a href="#/" class="filter__link selected" data-cy="FilterLinkAll">
            All
          </a>

          <a href="#/active" class="filter__link" data-cy="FilterLinkActive">
            Active
          </a>

          <a
            href="#/completed"
            class="filter__link"
            data-cy="FilterLinkCompleted"
          >
            Completed
          </a>
        </nav>

        <!-- this button should be disabled if there are no completed todos -->
        <button
          type="button"
          class="todoapp__clear-completed"
          data-cy="ClearCompletedButton"
        >
          Clear completed
        </button>
      </footer>
    </div>

    <!-- DON'T use conditional rendering to hide the notification -->
    <!-- Add the 'hidden' class to hide the message smoothly -->
    <div
      data-cy="ErrorNotification"
      class="notification is-danger is-light has-text-weight-normal"
    >
      <button data-cy="HideErrorButton" type="button" class="delete"></button>
      <!-- show only one message at a time -->
      Unable to load todos
      <br />
      Title should not be empty
      <br />
      Unable to add a todo
      <br />
      Unable to delete a todo
      <br />
      Unable to update a todo
    </div>
  </div>
</template>
