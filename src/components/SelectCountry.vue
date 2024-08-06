<script lang="ts" setup>
import { ref, onMounted } from 'vue';
import {
  Select,
  SelectContent,
  SelectGroup,
  SelectItem,
  SelectLabel,
  SelectTrigger,
  SelectValue,
} from '@/components/ui/select';

// 定义发射事件
const emit = defineEmits(['updateCountry']);

const selectedCountry = ref('');
const countries = ref<{ code: string; name: string }[]>([]);

const fetchCountries = async () => {
  try {
    const response = await fetch('/data/2016000606.json');  // 修改路径到实际文件位置
    const json = await response.json();
    const classObj = json.GET_STATS_DATA.STATISTICAL_DATA.CLASS_INF.CLASS_OBJ.find(
      (obj: any) => obj["@id"] === "cat03"
    );

    if (classObj && classObj.CLASS) {
      countries.value = classObj.CLASS
        .filter((country: any) => country["@name"] !== "台湾")  // 过滤掉台湾
        .map((country: any) => ({
          code: country["@code"],
          name: country["@name"],
        }));
    } else {
      console.error('找不到 cat03 对象或 CLASS:', json);
    }
  } catch (error) {
    console.error('Error fetching countries:', error);
  }
};

function handleCountryChange(value: string) {
  selectedCountry.value = value;
  // 向父组件传递选中的国家
  emit('updateCountry', value);
}

onMounted(fetchCountries);
</script>

<template>
  <Select @update:modelValue="handleCountryChange">
    <SelectTrigger class="w-[280px]">
      <SelectValue placeholder="Select a country" />
    </SelectTrigger>
    <SelectContent>
      <SelectGroup>
        <SelectLabel>Countries</SelectLabel>
        <template v-for="country in countries" :key="country.code">
          <SelectItem :value="country.code">{{ country.name }}</SelectItem>
        </template>
      </SelectGroup>
    </SelectContent>
  </Select>
</template>
