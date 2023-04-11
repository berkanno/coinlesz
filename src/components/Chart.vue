<template>
  <div>
    <v-container>
      <ag-charts-vue :options="options"></ag-charts-vue>
    </v-container>
  </div>
</template>
<script lang="ts">
import axios from "axios";
import "ag-charts-community";
import { AgChartsVue } from "ag-charts-vue3";
export default {
  data() {
    return {
      options: null,
      totalCount: 0 as any,
      totalCountName: "" as any,
      chartsData: [{ title: "", count: 0, countName: "" }] as any,
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
    totalCountCalculate() {
      this.chartsData.map((x: any) => {
        this.totalCount += x.count;
      });
      this.totalCountName = this.getNumberUnit(this.totalCount);
      console.log(this.totalCount, this.totalCountName);
    },
  },
  components: {
    AgChartsVue,
  },
  beforeMount() {
    axios
      .get("https://api.coincap.io/v2/assets")
      .then((res: any) => {
        res.data.data
          .filter((x: any) => x)
          .map((x: any) => {
            if (x.rank < 7) {
              this.chartsData[Number(x.rank) - 1].title = x.name;
              // x.marketCapUsd = this.getNumberUnit(x.marketCapUsd);
              this.chartsData[Number(x.rank) - 1].count = Number(
                x.marketCapUsd
              );
              this.chartsData[Number(x.rank) - 1].countName =
                this.getNumberUnit(this.chartsData[Number(x.rank) - 1].count);
              this.chartsData.push({ title: "", count: 0, countName: "" });
            } else {
              this.chartsData[6].title = "Others";
              this.chartsData[6].count += Number(x.marketCapUsd);
              this.chartsData[6].countName = this.getNumberUnit(
                this.chartsData[6].count
              );
            }
          });
        this.totalCountCalculate();
      })
      .catch((e: any) => console.log("hata"));
  },
  created() {
    const data = this.chartsData;

    const numFormatter = new Intl.NumberFormat("en-US");

    const total = data.reduce((sum: any, d: any) => sum + d["count"], 0);
    this.options = {
      autoSize: true,
      data,
      title: {
        text: "MARKET CAP",
        fontSize: 18,
        color: "red",
      },
      series: [
        {
          type: "pie",
          calloutLabelKey: "type",
          fillOpacity: 0.9,
          strokeWidth: 0,
          angleKey: "count",
          sectorLabelKey: "count",
          calloutLabel: {
            enabled: false,
          },
          sectorLabel: {
            color: "white",
            fontWeight: "bold",
            formatter: ({ datum, sectorLabelKey }: any) => {
              const value = datum[sectorLabelKey];
              return numFormatter.format(value);
            },
          },
          innerRadiusRatio: 0.5,
          innerLabels: [
            {
              text: numFormatter.format(total),
              fontSize: 24,
              fontWeight: "bold",
            },
            {
              text: "Total",
              fontSize: 16,
            },
          ],
          highlightStyle: {
            item: {
              fillOpacity: 0,
              stroke: "#535455",
              strokeWidth: 1,
            },
          },
          tooltip: {
            renderer: ({
              datum,
              calloutLabelKey,
              title,
              sectorLabelKey,
            }: any) => {
              return {
                title,
                content: `${datum[calloutLabelKey]}: ${numFormatter.format(
                  datum[sectorLabelKey]
                )}`,
              };
            },
          },
        },
      ],
    } as any;
  },
  watch: {
    chartsData: {
      handler(newValue, oldValue) {
        console.log(newValue, oldValue);
      },
      deep: true,
    },
  },
};
</script>
