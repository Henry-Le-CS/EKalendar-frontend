<template>
  <div
    class="flex items-center px-4 justify-center h-[100vh] border border-1 w-[100%]"
  >
    <div
      class="h-max flex items-center justify-center w-full px-4 py-8 lg:w-[50%] border border-1 rounded-md"
    >
      <CalendarForm class="w-full flex items-center justify-center" />
    </div>
  </div>
</template>

<script setup lang="ts">
import CalendarForm from './components/calendar-form/index.vue';
</script>
