<script setup>
  import { defineProps, defineEmits } from 'vue';


  const emit = defineEmits(['update:modelValue']);
  const { modelValue } = defineProps({
    modelValue: String
  });
</script>

<template>
  <nav class="filter">
    <a
      href="#/"
      class="filter__link"
      :class="{ selected: modelValue === 'all' }"
      @click="emit('update:modelValue', 'all')"
    >
      All
    </a>
    <a
      href="#/active"
      class="filter__link"
      :class="{ selected: modelValue === 'active' }"
      @click="emit('update:modelValue', 'active')"
    >
      Active
    </a>
    <a
      href="#/completed"
      class="filter__link"
      :class="{ selected: modelValue === 'completed' }"
      @click="emit('update:modelValue', 'completed')"
    >
      Completed
    </a>
  </nav>
</template>
