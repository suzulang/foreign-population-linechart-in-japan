<template>
  <div>
    <LineChart :chartData="chartData" />
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, watch } from 'vue';
import { LineChart } from 'vue-chart-3';
import { Chart, registerables } from 'chart.js';

Chart.register(...registerables);

interface DataPoint {
  "@tab": string;
  "@cat01": string;
  "@cat02": string;
  "@cat03": string;
  "@time": string;
  "@unit": string;
  "$": string;
}

export default defineComponent({
  name: 'LineChartComponent',
  components: { LineChart },
  props: {
    selectedCountry: {
      type: String,
      required: true,
    },
  },
  setup(props) {
    const chartData = ref({
      labels: [] as string[],
      datasets: [
        {
          label: '在留人数',
          data: [] as number[],
          backgroundColor: 'rgba(75, 192, 192, 0.2)',
          borderColor: 'rgba(75, 192, 192, 1)',
          borderWidth: 1,
        },
      ],
    });

    const jsonFiles = [
      '2012000606.json', '2012001212.json',
      '2013000606.json', '2013001212.json',
      '2014000606.json', '2014001212.json',
      '2015000606.json', '2015001212.json',
      '2016000606.json', '2016001212.json',
      '2017000606.json'
    ];

    const fetchData = async () => {
      try {
        const labels: string[] = [];
        const data: number[] = [];

        for (const file of jsonFiles) {
          const response = await fetch(`/data/${file}`);
          const json = await response.json();
          if (!json.GET_STATS_DATA || !json.GET_STATS_DATA.STATISTICAL_DATA || !json.GET_STATS_DATA.STATISTICAL_DATA.DATA_INF || !json.GET_STATS_DATA.STATISTICAL_DATA.DATA_INF.VALUE) {
            console.error('JSON 数据结构不符合预期:', json);
            continue;
          }

          const dataPoints: DataPoint[] = json.GET_STATS_DATA.STATISTICAL_DATA.DATA_INF.VALUE;
          console.log('Data Points:', dataPoints); // 打印 Data Points

          const countryCode = props.selectedCountry || '000';
          const countryData = dataPoints.find(point => point["@cat03"] === countryCode);
          console.log('Country Data:', countryData); // 打印 Country Data

          if (countryData && countryData["$"]) {
            const yearMonth = file.substring(0, 8);
            const year = yearMonth.substring(0, 4);
            const month = yearMonth.substring(6, 8);

            labels.push(`${year}-${month.padStart(2, '0')}`);
            data.push(Number(countryData["$"]));
          } else {
            console.error('找不到国家数据或 $ 字段:', countryData);
          }
        }

        chartData.value.labels = labels;
        chartData.value.datasets[0].data = data;
      } catch (error) {
        console.error('Error fetching JSON:', error);
      }
    };

    watch(() => props.selectedCountry, fetchData, { immediate: true });

    return { chartData };
  },
});
</script>
