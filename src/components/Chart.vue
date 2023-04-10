<template></template>
<script lang="ts">
import axios from "axios";
import "ag-charts-community";
import { AgChartsVue } from "ag-charts-vue3";
export default {
  data() {
    return {
      totalCount: 0 as any,
      chartsData: [{ title: "", count: 0 }] as any,
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
  components: {
    AgChartsVue,
  },
  mounted() {
    axios.get("https://api.coincap.io/v2/assets").then((res: any) =>
      res.data.data
        .filter((x: any) => x.rank < 7)
        .map((x: any) => {
          this.chartsData[Number(x.rank) - 1].title = x.name;
          x.marketCapUsd = this.getNumberUnit(x.marketCapUsd);
          this.chartsData[Number(x.rank) - 1].count = x.marketCapUsd;
          this.chartsData[Number(x.rank)].push("{ title: '', count: 0 }");
        })
    );
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
