<template>
  <v-app>
    <v-app-bar style="position: fixed" flat height="100%">
      <Toolbar />



    </v-app-bar>



    <v-container class="mt-16">
      <v-tabs v-model="tabs" bg-color="black" class="text-red">
								<v-tab value="grid"><span style="letter-spacing:10px"> Coins
                  </span><v-icon icon="mdi-bitcoin" class="ml-3" color="white"></v-icon></v-tab>
								<v-tab value="chart"> <span style="letter-spacing:5px">Market Cap
                  </span> <v-icon icon="mdi-currency-usd" class="ml-3" color="white"></v-icon></v-tab>
							</v-tabs>
<v-window v-model="tabs" show-arrows="true">
  
  <v-window-item value="grid">
          <v-row class="mt-16 d-flex justify-center">
        <v-col cols="10 fill-height" class="text-center">
          <ag-grid-vue
            style="height: 470px; width: 100%"
            class="ag-theme-alpine font-weight-light text-caption overflow-y text-purple-darken-3"
            :columnDefs="columnDefs"
            :defaultColDef="defaultColDef"
            :rowData="rowData"
            :rowSelection="rowSelection"
            :rowHeight="40"
          >
          </ag-grid-vue>
        </v-col>
      </v-row>
  </v-window-item><v-window-item value="chart">
      <Chart />

  </v-window-item>
</v-window>

    </v-container>
  </v-app>
</template>

<script lang="ts">
import axios from "axios";
import "ag-grid-community/styles/ag-grid.css";
import "ag-grid-community/styles/ag-theme-alpine.css";
import { AgGridVue } from "ag-grid-vue3";
import Toolbar from "@/components/Toolbar.vue";
import Chart from "@/components/Chart.vue";
export default {
  components: {
    AgGridVue,
    Toolbar,
    Chart,
  },
  data() {
    return {
      tabs: "grid",
      columnDefs: [
        {
          headerName: "Rank",
          field: "rank",
          cellStyle: {
            textAlign: "center",
          },
          resizable: true,
        },
        {
          headerName: "Name",
          field: "id",
          resizable: true,
          cellStyle: {
            fontFamily: "Times New Roman",
            fontSize: "13px",
            textAlign: "center",
          },
        },
        { headerName: "Price", field: "priceUsd", resizable: true },
        { headerName: "Market Cap", field: "marketCapUsd", resizable: true },
        { headerName: "VWAP (24Hr)", field: "vwap24Hr", resizable: true },
        {
          headerName: "Supply",
          field: "supply",
          resizable: true,
        },
        {
          headerName: "Volume (24Hr)",
          field: "volumeUsd24Hr",
          resizable: true,
        },
        {
          headerName: "Change (24Hr)",
          field: "changePercent24Hr",
          resizable: true,
          cellStyle: (params: any) => {
            if (Number(params.data.changePercent24Hr.slice(0, -1)) > 0) {
              return { color: "green", textAlign: "center", fontWeight: "500" };
            } else {
              return { color: "red", textAlign: "center", fontWeight: "500" };
            }
          },
        },
      ] as any,
      rowData: [] as any,
      defaultColDef: {
        sortable: true,
        filter: true,
      },
      rowSelection: null,
    };
  },
  methods: {
    calıstır() {
      this.rowData.map((x: any) => {
        x.id = x.id + "  (  " + x.symbol + "  )  ";
        x.volumeUsd24Hr =
          "$" + String(this.getNumberUnit(Number(x.volumeUsd24Hr)));
        x.marketCapUsd =
          "$" + String(this.getNumberUnit(Number(x.marketCapUsd)));
        String((x.supply = this.getNumberUnit(Number(x.supply))));
        x.changePercent24Hr =
          String(Number(x.changePercent24Hr).toFixed(2)) + "%";
        x.priceUsd = "$" + String(Number(x.priceUsd).toFixed(2));
        x.vwap24Hr = "$" + String(Number(x.vwap24Hr).toFixed(2));
      });
    },

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
  mounted() {
    axios
      .get("https://api.coincap.io/v2/assets")
      .then((res: any) => {
        this.rowData = res.data.data;
        this.calıstır();
      })
      .catch((e: any) => console.log("err"));
  },
  created() {
    this.rowSelection = "multiple" as any;
  },
};
</script>
<style scoped>
.ag-theme-alpine {
  --ag-borders: none;
  --ag-header-foreground-color: rgb(255, 0, 0);
  --ag-header-background-color: rgb(0, 0, 0);
  --ag-header-cell-hover-background-color: rgb(255, 255, 255);
  --ag-header-cell-moving-background-color: rgb(0, 0, 0);
  --ag-selected-row-background-color: rgba(38, 0, 255, 0.269);
}
</style>
