<script setup>
import { defineProps, defineEmits, ref, nextTick } from "vue";

// const { todo } = defineProps(["todo"]);
const emit = defineEmits(["delete", "update"]);
const props = defineProps(['todo']);

const editing = ref(false);
const titleField = ref(null);

const startEditing = async () => {
  editing.value = true;

  await nextTick();

  if (titleField.value) {
    titleField.value.focus();
  }
};

const newTitle = ref(props.todo.title);

const rename = () => {
  if(!editing.value) return;

  editing.value = false;

  if (newTitle.value === props.todo.title) {
    return;
  }

  if(!newTitle.value) {
    emit('delete');
    return;
  }
  
  emit('update', { ...props.todo, title: newTitle.value });
}
</script>

<template>
  <div class="todo" :class="{ completed: props.todo.completed }">
    <label class="todo__status-label">
      <input
        type="checkbox"
        class="todo__status"
        :checked="props.todo.completed"
        @change="$emit('update', { ...props.todo, completed: !props.todo.completed })"
      />
    </label>

    <!-- show when todo is being edited -->
    <form v-if="editing" @submit.prevent="rename">
      <input
      ref="titleField"
      v-model.trim="newTitle"
      class="todo__title-field"
      placeholder="Empty todo will be deleted"
      @keyup.escape="editing = false"
      @blur="rename"
      />
    </form>

    <template v-else>
      <span class="todo__title" @dblclick="startEditing">{{ todo.title }}</span>
      <button class="todo__remove" @click="emit('delete')">×</button>
    </template>

    <!-- add `is-active` class when todo being processed -->
    <div class="modal overlay" :class="{ 'is-active': editing }">
      <div class="modal-background has-background-white-ter"></div>
      <div class="loader"></div>
    </div>
  </div>
</template>
