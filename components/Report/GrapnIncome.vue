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
    await this.getGraphGender();
    await this.getGraphIncome();
    await this.getGraphOutcome();
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
  chartIncome: any = {
    chart: {
      height: 350,
      type: "line",
      zoom: {
        enabled: false,
      },
    },
    colors: ["#546E7A"],
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
    colors: ["#546E7A"],
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
  async getGraphGender() {
    let result: any = _.chain(this.lists)
      // Group the elements of Array based on `color` property
      .groupBy("date")
      // `key` is group's name (color), `value` is the array of objects
      .map((value, key) => ({ date: key, data: value }))
      .value();
    let labels = [];
    let series = [];
    for (let i = 0; i < result.length; i++) {
      let income = _.sumBy(_.filter(result[i].data, { type: true }), "value");
      let outcome = _.sumBy(_.filter(result[i].data, { type: false }), "value");
      labels.push(result[i].date);
      series.push(income - outcome);
    }
    this.series.push({
      name: "รายได้",
      data: series,
    });
    this.chartOptions.xaxis.categories = labels;
  }
  async getGraphIncome() {
    let result: any = _.chain(this.lists)
      // Group the elements of Array based on `color` property
      .groupBy("date")
      // `key` is group's name (color), `value` is the array of objects
      .map((value, key) => ({ date: key, data: value }))
      .value();
    let labels = [];
    let seriesIncome = [];
    for (let i = 0; i < result.length; i++) {
      let income = _.sumBy(_.filter(result[i].data, { type: true }), "value");
      labels.push(result[i].date);
      seriesIncome.push(income);
    }
    this.seriesIncome.push({
      name: "รายรับ",
      data: seriesIncome,
    });
    this.chartIncome.xaxis.categories = labels;
  }
  async getGraphOutcome() {
    let result: any = _.chain(this.lists)
      // Group the elements of Array based on `color` property
      .groupBy("date")
      // `key` is group's name (color), `value` is the array of objects
      .map((value, key) => ({ date: key, data: value }))
      .value();
    let labels = [];
    let seriesOutcome = [];
    for (let i = 0; i < result.length; i++) {
      let outcome = _.sumBy(_.filter(result[i].data, { type: false }), "value");
      labels.push(result[i].date);
      seriesOutcome.push(outcome);
    }
    // this.seriesOutcome = seriesOutcome;
    this.seriesOutcome.push({
      name: "รายจ่าย",
      data: seriesOutcome,
    });
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
