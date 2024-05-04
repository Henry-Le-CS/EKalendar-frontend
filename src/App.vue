<template>
  <div
    class="flex flex-col items-center gap-4 px-4 justify-center h-[100vh] border border-1 w-[100%]"
  >
    <div class="font-mono font-thin text-[#006DCC]">
      <span class="text-6xl text-[#005f69]">E</span>
      <span class="text-6xl">K</span>
      <span class="text-6xl">alendar</span>
    </div>
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
