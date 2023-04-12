<template>
  <div>
  </div>
</template>
<script lang="ts">
import axios from "axios";

export default {
  name: "Chart",
  components: {
    
  },
  data() {
    return {
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
