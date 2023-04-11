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
    totalCountCalculate() {
      this.chartsData.map((x: any) => {
        this.totalCount += x.count;
      });
      this.totalCountName = this.getNumberUnit(this.totalCount);
      console.log(this.totalCount, this.totalCountName);
      this.calıstı();
    },
    getData(){
      return this.chartsData
    },
    calıstı(){
      const numFormatter = new Intl.NumberFormat('en-US');
      this.options = {
      autoSize: true,
      title: {
        text: 'Religions of London Population',
        fontSize: 18,
      },
      footnote: {
        text: 'Source: Office for National Statistics',
      },
      padding: {
        top: 32,
        right: 20,
        bottom: 32,
        left: 20,
      },
      series: [
        {
          data: this.getData(),
          type: 'pie',
          calloutLabelKey: 'religion',
          sectorLabelKey: 'population',
          angleKey: 'population',
          calloutLabel: {
            minAngle: 0,
          },
          sectorLabel: {
            color: 'white',
            fontWeight: 'bold',
            formatter: ({ datum, sectorLabelKey } : any) => {
              return numFormatter.format(datum[sectorLabelKey]);
            },
          },
          calloutLine: {
            strokeWidth: 2,
          },
          fills: [
            '#49afda',
            '#57cc8b',
            '#bcdf72',
            '#fbeb37',
            '#f4b944',
            '#fb7451',
            '#72508c',
            '#b7b5ba',
          ],
          strokeWidth: 0,
          tooltip: {
            renderer: ({ datum, color, calloutLabelKey, sectorLabelKey } : any) => {
              return [
                `<div style="background-color: ${color}; padding: 4px 8px; border-top-left-radius: 5px; border-top-right-radius: 5px; font-weight: bold; color: white;">${datum[calloutLabelKey]}</div>`,
                `<div style="padding: 4px 8px">${numFormatter.format(
                  datum[sectorLabelKey]
                )}</div>`,
              ].join('\n');
            },
          },
          highlightStyle: {
            item: {
              fillOpacity: 0,
              stroke: '#535455',
              strokeWidth: 1,
            },
          } as any,
        },
      ],
      legend: {
        enabled: false,
      },
    };
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
              this.chartsData[Number(x.rank) - 1].name = x.name;
              // x.marketCapUsd = this.getNumberUnit(x.marketCapUsd);
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
        this.totalCountCalculate();
      })
      .catch((e: any) => console.log("hata"));
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
