<template>
  <div v-if="response">
    <h2 class="p-4 text-xl font-bold">สรุปค่าใช้จ่าย</h2>

    <apexchart
      type="line"
      height="350"
      :options="chartOptions"
      :series="series"
    ></apexchart>
    <h2 class="p-4 text-xl font-bold">รายรับ</h2>

    <apexchart
      type="line"
      height="350"
      :options="chartIncome"
      :series="seriesIncome"
    ></apexchart>
    <h2 class="p-4 text-xl font-bold">รายจ่าย</h2>

    <apexchart
      type="line"
      height="350"
      :options="chartOutcome"
      :series="seriesOutcome"
    ></apexchart>
  </div>
</template>

<script lang="ts">
import { Component, Vue, Watch, Prop } from "nuxt-property-decorator";
import { Core } from "@/vuexes/core";
import { Auth } from "@/vuexes/auth";
import _ from "lodash";
import VueApexCharts from "vue-apexcharts";

@Component({
  components: { apexchart: VueApexCharts },
})
export default class MyComponent extends Vue {
  lists: any = [];

  user: any = {};
  response: boolean = false;
  text: string = "hello world";

  gender: any = [];

  async created() {
    this.user = await Auth.getUser();
    this.lists = await Core.getHttp(
      `/api/v1/farmer/income/?user=${this.user.id}`
    );

    this.buildCharts();
    this.response = true;

    console.log(this.lists);
    console.log(this.chartOptions);
  }

  series: any = [];
  seriesIncome: any = [];
  seriesOutcome: any = [];
  chartOptions: any = {
    chart: {
      height: 350,
      type: "line",
      zoom: {
        enabled: false,
      },
    },
    colors: ["#1E88E5"],
    dataLabels: {
      enabled: false,
    },
    stroke: {
      curve: "straight",
    },

    grid: {
      row: {
        colors: ["#f3f3f3", "transparent"], // takes an array which will be repeated on columns
        opacity: 0.5,
      },
    },
    xaxis: {
      categories: [],
    },
    yaxis: {
      labels: {
        formatter: (value: number) => `${value.toLocaleString()}`,
      },
    },
    tooltip: {
      y: {
        formatter: (value: number) => `฿ ${value.toLocaleString()}`,
      },
    },
  };
  chartIncome: any = {
    chart: {
      height: 350,
      type: "line",
      zoom: {
        enabled: false,
      },
    },
    colors: ["#2E7D32"],
    dataLabels: {
      enabled: false,
    },
    stroke: {
      curve: "straight",
    },

    grid: {
      row: {
        colors: ["#f3f3f3", "transparent"], // takes an array which will be repeated on columns
        opacity: 0.5,
      },
    },
    xaxis: {
      categories: [],
    },
  };
  chartOutcome: any = {
    chart: {
      height: 350,
      type: "line",
      zoom: {
        enabled: false,
      },
    },
    colors: ["#E53935"],
    dataLabels: {
      enabled: false,
    },
    stroke: {
      curve: "straight",
    },

    grid: {
      row: {
        colors: ["#f3f3f3", "transparent"], // takes an array which will be repeated on columns
        opacity: 0.5,
      },
    },
    xaxis: {
      categories: [],
    },
  };
  getGroupedByDate() {
    const grouped = _.chain(this.lists)
      .groupBy("date")
      .map((value, key) => ({ date: key, data: value }))
      .sortBy("date")
      .value();
    return grouped;
  }

  buildCharts() {
    const grouped = this.getGroupedByDate();
    const labels = grouped.map((row: any) => row.date);

    const incomeData = grouped.map((row: any) =>
      _.sumBy(_.filter(row.data, { type: true }), "value")
    );
    const outcomeData = grouped.map((row: any) =>
      _.sumBy(_.filter(row.data, { type: false }), "value")
    );
    const netData = incomeData.map((inc: number, idx: number) => inc - outcomeData[idx]);

    this.series = [
      {
        name: "สุทธิ (รายรับ - รายจ่าย)",
        data: netData,
      },
    ];
    this.chartOptions.xaxis.categories = labels;

    this.seriesIncome = [
      {
        name: "รายรับ",
        data: incomeData,
      },
    ];
    this.chartIncome.xaxis.categories = labels;

    this.seriesOutcome = [
      {
        name: "รายจ่าย",
        data: outcomeData,
      },
    ];
    this.chartOutcome.xaxis.categories = labels;
  }
}
</script>


<style>
.bgh {
  background: rgb(53, 184, 140);
  background: linear-gradient(
    180deg,
    rgba(53, 184, 140, 1) 18%,
    rgba(17, 140, 87, 1) 100%
  );
}
</style>
