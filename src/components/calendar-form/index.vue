<template>
  <div class="flex flex-col gap-8 items-start justify-start w-full lg:w-[60%]">
    <div class="flex flex-col gap-3 w-full">
      <h1 class="font-bold h-max">Bạn là sinh viên trường nàooo ?</h1>

      <a-select
        class="w-full"
        ref="select"
        v-model:value="university"
        :options="uniOptions"
        @change="handleChange"
      ></a-select>
    </div>

    <div class="flex flex-col items-center justify-start gap-4">
      <p class="text-sm">
        <span class="font-bold text-black">Hint: </span>Bạn vào
        <a
          class="text-[#005f69] font-bold underline"
          href="https://student.ueh.edu.vn/Home/Schedules"
          >https://student.ueh.edu.vn/Home/Schedules</a
        >, chọn
        <span class="text-[#005f69] font-bold underline">TKB Thứ - Tiết,</span>
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

    <div
      v-if="calendar && !error"
      class="flex flex-col items-start justify-start gap-2 w-full"
    >
      <h1 class="font-bold">Nhập tên lịch ở đây nè:</h1>
      <a-input
        v-model:value="calendarName"
        placeholder="Nhập tên lịch đi nè"
        required
      />
    </div>

    <a-button
      :loading="loading"
      :disabled="!calendar || loading || !calendarName || error"
      id="g-auth2"
      type="primary"
      class="bg-[#005f69] font-bold"
      @click="handleAddToCalendar"
      >Thêm vào Google Calendar</a-button
    >
  </div>
</template>

<script lang="ts" setup>
import { ref } from 'vue';
import type { SelectProps } from 'ant-design-vue';
import { googleSdkLoaded } from 'vue3-google-login';
import { TokenResponse } from './types';
import { endpoint } from '../../common/endpoint';

const university = ref('ueh');
const uniOptions = ref<SelectProps['options']>([
  {
    value: 'ueh',
    label: 'Đại học Kinh tế TP.HCM',
  },
]);

const calendarName = ref('');
const pageText = ref('');
const calendar = ref('');
const error = ref('');
const loading = ref(false);

const handleChange = (value: string) => {
  console.log(`selected ${value}`);
};

const handleInputChange = () => {
  const lines = pageText.value.split('\n');
  loading.value = true;

  fetch(`${endpoint}/calendar/`, {
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
      calendar.value = r.Data;
    })
    .catch(err => {
      calendar.value = '';
      error.value = 'Có lỗi xảy ra: ' + err?.message || err;
    });

  loading.value = false;
};

const callback = (response: TokenResponse) => {
  const { access_token: AccessToken, token_type: TokenType } = response;

  if (!calendarName.value) {
    error.value = 'Nhập tên lịch voooooo';
    return;
  }

  fetch(`${endpoint}/calendar/add`, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      Token: {
        AccessToken,
        TokenType,
      },
      Ics: calendar.value,
      CalendarName: calendarName.value,
    }),
  })
    .then(res => res.json())
    .then(r => {
      if (r.Error) {
        error.value = r.Error;
        console.log(r);
        return;
      }

      window.alert('Thêm lịch thành công !');
    })
    .catch(err => {
      calendar.value = '';
      error.value = 'Có lỗi xảy ra: ' + err?.message || err;
    });
};

function handleAddToCalendar() {
  googleSdkLoaded(google => {
    google.accounts.oauth2
      .initTokenClient({
        client_id: import.meta.env.VITE_GOOGLE_CLIENT_ID as string,
        scope: 'https://www.googleapis.com/auth/calendar',
        callback,
      })
      .requestAccessToken();
  });
}
</script>
