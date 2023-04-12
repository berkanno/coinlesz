<template>
  <v-row justify="center">
    <v-col cols="7" class="mt-7">
      <apexchart
        type="donut"
        width="100%"
        height="500"
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
      chartsData: [{ name: "", count: 0 }] as any,
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
                width: "100%",
              },
              legend: {
                position: "left",
                offsetY: 200,
              },
            },
          },
        ],
        plotOptions: {
          pie: {
            donut: {
              size: "65%",
              labels: {
                show: true,
                total: {
                  show: true,
                  formatter: function (value: any) {
                    const total = value.config.series.reduce(
                      (accumulator: any, currentValue: any) =>
                        accumulator + currentValue,
                      0
                    );
                    return Math.abs(Number(total)) >= 1.0e9
                      ? (Math.abs(Number(total)) / 1.0e9).toFixed(2) + " B"
                      : // Six Zeroes for Millions
                      Math.abs(Number(total)) >= 1.0e6
                      ? (Math.abs(Number(total)) / 1.0e6).toFixed(2) + " M"
                      : // Three Zeroes for Thousands
                      Math.abs(Number(total)) >= 1.0e3
                      ? (Math.abs(Number(total)) / 1.0e3).toFixed(2) + " K"
                      : Math.abs(Number(total));
                  },
                },
                value: {
                  formatter: function (value: any) {
                    return Math.abs(Number(value)) >= 1.0e9
                      ? (Math.abs(Number(value)) / 1.0e9).toFixed(2) + " B"
                      : // Six Zeroes for Millions
                      Math.abs(Number(value)) >= 1.0e6
                      ? (Math.abs(Number(value)) / 1.0e6).toFixed(2) + " M"
                      : // Three Zeroes for Thousands
                      Math.abs(Number(value)) >= 1.0e3
                      ? (Math.abs(Number(value)) / 1.0e3).toFixed(2) + " K"
                      : Math.abs(Number(value));
                  },
                },
              },
            },
          },
        },
        title: {
          text: "Market Cap",
          align: "center",
          style: {
            color: "red",
            fontWeight: "thin",
            fontSize: "40px",
          },
        },
      },
    };
  },
  methods: {
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
                this.chartsData.push({ name: "", count: 0 });
            } else {
              this.chartsData[6].name = "Others";
              this.chartsData[6].count += Number(x.marketCapUsd);
            }
          });
        this.getLabels();
        this.getSeries();
      })
      .catch((e: any) => console.log("hata"));
  },
};
</script>
