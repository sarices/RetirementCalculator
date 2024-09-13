<script setup lang="ts">
import { ref, computed } from 'vue';
import { Menu, MenuButton, MenuItems, MenuItem } from '@headlessui/vue';
import { ChevronDownIcon, CalendarIcon } from '@heroicons/vue/20/solid';
import DatePicker from '@vuepic/vue-datepicker';
import '@vuepic/vue-datepicker/dist/main.css';

interface RetirementInfo {
  retirementDate: Date;
  age: number;
}

const birthDate = ref<Date | null>(null);
const gender = ref<string>('');

const calculateRetirement = computed((): RetirementInfo | null => {
  if (!birthDate.value || !gender.value) return null;

  const birth = birthDate.value;
  const implementationDate = new Date('2025-01-01');
  let retirementAge: number;
  let delayMonths = 0;

  if (gender.value === 'male') {
    retirementAge = 60;
    while (delayMonths < 36) {
      const currentRetirementDate = new Date(
        birth.getFullYear() + retirementAge,
        birth.getMonth(),
        birth.getDate()
      );
      if (currentRetirementDate >= implementationDate) {
        delayMonths += 1;
      }
      if (delayMonths % 4 === 0) {
        retirementAge += 1;
      }
      if (retirementAge >= 63) break;
    }
  } else {
    const originalRetirementAge = birth.getFullYear() < 1965 ? 50 : 55;
    retirementAge = originalRetirementAge;
    const monthIncrement = originalRetirementAge === 50 ? 2 : 4;
    while (delayMonths < (originalRetirementAge === 50 ? 60 : 36)) {
      const currentRetirementDate = new Date(
        birth.getFullYear() + retirementAge,
        birth.getMonth(),
        birth.getDate()
      );
      if (currentRetirementDate >= implementationDate) {
        delayMonths += 1;
      }
      if (delayMonths % monthIncrement === 0) {
        retirementAge += 1;
      }
      if (retirementAge >= (originalRetirementAge === 50 ? 55 : 58)) break;
    }
  }

  const retirementDate = new Date(
    birth.getFullYear() + retirementAge,
    birth.getMonth(),
    birth.getDate()
  );
  const age = retirementAge;

  return { retirementDate, age };
});

const setGender = (value: string) => {
  gender.value = value;
};
</script>

<template>
  <div class="max-w-md mx-auto mt-10 p-6 bg-white rounded-lg shadow-xl">
    <h2 class="text-2xl font-bold mb-6 text-gray-800">退休年龄计算器</h2>

    <div class="mb-4">
      <label class="block text-sm font-medium text-gray-700 mb-2">
        出生日期
      </label>
      <DatePicker
        v-model="birthDate"
        :enable-time-picker="false"
        locale="zh"
        :format="(date) => date.toLocaleDateString('zh-CN')"
        input-class-name="w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500"
      >
        <template #input-icon>
          <CalendarIcon class="w-5 h-5 text-gray-400" />
        </template>
      </DatePicker>
    </div>

    <div class="mb-6">
      <label class="block text-sm font-medium text-gray-700 mb-2"> 性别 </label>
      <Menu as="div" class="relative inline-block text-left w-full">
        <div>
          <MenuButton
            class="inline-flex w-full justify-between rounded-md border border-gray-300 bg-white px-4 py-2 text-sm font-medium text-gray-700 shadow-sm hover:bg-gray-50 focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:ring-offset-2 focus:ring-offset-gray-100"
          >
            {{ gender ? (gender === 'male' ? '男' : '女') : '请选择' }}
            <ChevronDownIcon class="-mr-1 ml-2 h-5 w-5" aria-hidden="true" />
          </MenuButton>
        </div>

        <transition
          enter-active-class="transition ease-out duration-100"
          enter-from-class="transform opacity-0 scale-95"
          enter-to-class="transform opacity-100 scale-100"
          leave-active-class="transition ease-in duration-75"
          leave-from-class="transform opacity-100 scale-100"
          leave-to-class="transform opacity-0 scale-95"
        >
          <MenuItems
            class="absolute right-0 z-10 mt-2 w-full origin-top-right rounded-md bg-white shadow-lg ring-1 ring-black ring-opacity-5 focus:outline-none"
          >
            <div class="py-1">
              <MenuItem v-slot="{ active }">
                <a
                  href="#"
                  :class="[
                    active ? 'bg-gray-100 text-gray-900' : 'text-gray-700',
                    'block px-4 py-2 text-sm',
                  ]"
                  @click="setGender('male')"
                >
                  男
                </a>
              </MenuItem>
              <MenuItem v-slot="{ active }">
                <a
                  href="#"
                  :class="[
                    active ? 'bg-gray-100 text-gray-900' : 'text-gray-700',
                    'block px-4 py-2 text-sm',
                  ]"
                  @click="setGender('female')"
                >
                  女
                </a>
              </MenuItem>
            </div>
          </MenuItems>
        </transition>
      </Menu>
    </div>

    <div v-if="calculateRetirement" class="bg-gray-100 p-4 rounded-md">
      <p class="text-lg font-semibold text-gray-800 mb-2">计算结果：</p>
      <p class="text-gray-600">
        预计退休日期:
        {{ calculateRetirement.retirementDate.toLocaleDateString('zh-CN') }}
      </p>
      <p class="text-gray-600">退休年龄: {{ calculateRetirement.age }} 岁</p>
    </div>
  </div>
</template>
