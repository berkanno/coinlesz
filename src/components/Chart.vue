<template>
  <v-row>

  <v-col cols="12" class="text-center">
    <apexchart
      type="donut"
      width="60%"
      :options="chartOptions"
      :series="series"
    ></apexchart>
  </v-col>
  </v-row>
</template>
<script lang="ts">
import axios from "axios";
import VueApexCharts from "vue3-apexcharts";

export default {
  name: "Chart",

  components: {
    apexchart: VueApexCharts,
  },
  data() {
    return {
      totalCount: 0 as any,
      totalCountName: "" as any,
      chartsData: [{ name: "", count: 0, countName: "" }] as any,
      series: [] as any,
      chartOptions: {
        chart: {
          type: "donut",
        },
        labels: [] as any,
        dataLabels: {
          dropShadow: {
            blur: 3,
            opacity: 0.8,
          },
        },
        responsive: [
          {
            breakpoint: 480,
            options: {
              chart: {
                width: 200,
              },
              legend: {
                position: "bottom",
              },
            },
          },
        ],
        plotOptions: {
          pie: {
            donut: {
              size:"65%",
              labels: {
                show: true,
                total: {
                  showAlways: true,
                  show: true,
                  
                },
              },
            },
          },
        },
        title: {
          text: "Market",
        },
      },
    };
  },
  methods: {
    getNumberUnit(labelValue: any) {
      // Nine Zeroes for Billions
      return Math.abs(Number(labelValue)) >= 1.0e9
        ? (Math.abs(Number(labelValue)) / 1.0e9).toFixed(2) + " B"
        : // Six Zeroes for Millions
        Math.abs(Number(labelValue)) >= 1.0e6
        ? (Math.abs(Number(labelValue)) / 1.0e6).toFixed(2) + " M"
        : // Three Zeroes for Thousands
        Math.abs(Number(labelValue)) >= 1.0e3
        ? (Math.abs(Number(labelValue)) / 1.0e3).toFixed(2) + " K"
        : Math.abs(Number(labelValue));
    },
    getLabels() {
      if (this.chartsData !== undefined) {
        this.chartsData.map((x: any) => this.chartOptions.labels.push(x.name));
      }
    },
    getSeries() {
      if (this.chartsData !== undefined) {
        this.chartsData.map((x: any) => this.series.push(x.count));
      }
    },
  },

  beforeMount() {
    axios
      .get("https://api.coincap.io/v2/assets")
      .then((res: any) => {
        res.data.data
          .filter((x: any) => x)
          .map((x: any) => {
            if (x.rank < 7) {
              this.chartsData[Number(x.rank) - 1].name = x.name;
              this.chartsData[Number(x.rank) - 1].count = Number(
                x.marketCapUsd
              );
              this.chartsData[Number(x.rank) - 1].countName =
                this.getNumberUnit(this.chartsData[Number(x.rank) - 1].count);
              this.chartsData.push({ name: "", count: 0, countName: "" });
            } else {
              this.chartsData[6].name = "Others";
              this.chartsData[6].count += Number(x.marketCapUsd);
              this.chartsData[6].countName = this.getNumberUnit(
                this.chartsData[6].count
              );
            }
          });
        this.chartsData.map((x: any) => {
          this.totalCount += x.count;
        });
        this.totalCountName = this.getNumberUnit(this.totalCount);
        this.getLabels();
        this.getSeries();
      })
      .catch((e: any) => console.log("hata"));
  },
  // watch: {
  //   options: {
  //     handler(newValue, oldValue) {
  //       console.log(newValue == oldValue);
  //     },
  //     deep: true,
  //   },
  // },
  //   totalCount: {
  //     handler(newValue, oldValue) {
  //       console.log(newValue, oldValue);
  //     },
  //     deep: true,
  //   },
  //   totalCountName: {
  //     handler(newValue, oldValue) {
  //       console.log(newValue, oldValue);
  //     },
  //     deep: true,
  //   },
  // },
};
</script>
