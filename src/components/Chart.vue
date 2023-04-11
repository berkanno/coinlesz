<template>
  <div>
    <ag-charts-vue :options="options"></ag-charts-vue>
  </div>
</template>
<script lang="ts">
import axios from "axios";
import { AgChartsVue } from "ag-charts-vue3";
import "ag-charts-community";
export default {
  name: "Chart",
  components: {
    AgChartsVue,
  },
  data() {
    return {
      options: {
        data: [] as any,
        autoSize: true,
        title: {
          text: "Market",
          fontSize: 18,
        },
        padding: {
          top: 32,
          right: 20,
          bottom: 32,
          left: 20,
        },
        series: [
          {
            type: "pie",
            calloutLabelKey: "name",
            sectorLabelKey: "countName",
            angleKey: "count",
            sectorLabel: {
              color: "white",
              fontWeight: "bold",
            },
            calloutLine: {
              strokeWidth: 2,
            },
            fills: [
              "#49afda",
              "#57cc8b",
              "#bcdf72",
              "#fbeb37",
              "#f4b944",
              "#fb7451",
              "#72508c",
            ],
            strokeWidth: 0,
            highlightStyle: {
              item: {
                fillOpacity: 0,
                stroke: "#535455",
                strokeWidth: 3,
              },
            },
          },
        ],
        legend: {
          enabled: true,
        },
      } as any,
      totalCount: 0 as any,
      totalCountName: "" as any,
      chartsData: [{ name: "", count: 0, countName: "" }] as any,
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
        this.options.data=Object.values(this.chartsData);
         
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
