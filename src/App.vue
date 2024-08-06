<template>
  <div id="app" class="flex flex-col items-center justify-center min-h-screen space-y-5">
    <div class="w-full flex justify-center">
      <SelectCountry @updateCountry="updateSelectedCountry" />
    </div>
    <div class="w-full flex justify-center mt-5">
      <LineChart :selectedCountry="selectedCountry" />
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue';
import SelectCountry from './components/SelectCountry.vue';
import LineChart from './components/LineChart.vue';

export default defineComponent({
  name: 'App',
  components: {
    SelectCountry,
    LineChart,
  },
  setup() {
    const selectedCountry = ref('');

    const updateSelectedCountry = (country: string) => {
      selectedCountry.value = country;
    };

    return {
      selectedCountry,
      updateSelectedCountry,
    };
  },
});
</script>

<style>
/* 你可以在这里添加全局样式 */
</style>
