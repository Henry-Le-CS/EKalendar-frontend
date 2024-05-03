<template>
  <div
    class="flex flex-col gap-8 items-center justify-center gap-1 w-full lg:w-[60%]"
  >
    <h1 class="font-bold h-max">Bạn là sinh viên trường nàooo ?</h1>
    <a-select
      class="w-full"
      ref="select"
      v-model:value="university"
      :options="uniOptions"
      @change="handleChange"
    ></a-select>

    <div class="flex flex-col items-center justify-start gap-4">
      <p class="text-sm">
        <span class="font-bold text-black">Hint: </span>Bạn vào
        <a
          class="text-[#005f69] font-bold underline"
          href="https://student.ueh.edu.vn/Home/Schedules"
          >https://student.ueh.edu.vn/Home/Schedules</a
        >
        rồi Ctrl A xong Ctrl C nhé. Xong rồi Ctrl V hết vào đây luôn. Đúng rồi,
        hết luônnnn !
      </p>

      <a-textarea
        v-model:value="pageText"
        @change="handleInputChange"
        placeholder="Ctrl A -> Ctrl C -> Ctrl V"
      />
    </div>

    <a-alert v-if="error" :message="error" type="error" show-icon />
    <div>
      <a-button type="primary" @click="handleAddToCalendar"
        >Add to calendar</a-button
      >
    </div>
  </div>
</template>

<script lang="ts" setup>
import { ref } from 'vue';
import type { SelectProps } from 'ant-design-vue';
import { authorizeUser } from './google';

const university = ref('ueh');
const uniOptions = ref<SelectProps['options']>([
  {
    value: 'ueh',
    label: 'Đại học Kinh tế TP.HCM',
  },
]);

const pageText = ref('');
const calendar = ref('');
const error = ref('');

const handleChange = (value: string) => {
  console.log(`selected ${value}`);
};

const handleInputChange = () => {
  const lines = pageText.value.split('\n');

  fetch('http://localhost:8080/calendar', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      Texts: lines,
      University: university.value,
      Semester: '232',
    }),
  })
    .then(res => res.json())
    .then(r => {
      if (r.Error) {
        error.value = r.Error;
        return;
      }

      error.value = '';
      calendar.value = r.Calendar;
    })
    .catch(err => {
      calendar.value = '';
      error.value = 'Có lỗi xảy ra: ' + err?.message || err;
    });
};

const handleAddToCalendar = () => {
  authorizeUser();
};
</script>
