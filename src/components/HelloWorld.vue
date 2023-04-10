<template>
  <v-app>
    <v-app-bar style="position: fixed" flat height="100%">
      <Toolbar />
    </v-app-bar>

    <v-container class="mt-16">
      <v-row class="mt-16 d-flex justify-center">
        <v-col cols="11 fill-height">
          <ag-grid-vue
            style="height: 700px; width: 100%"
            class="ag-theme-alpine font-weight-light text-overline overflow-y text-purple-darken-3"
            :columnDefs="columnDefs"
            :defaultColDef="defaultColDef"
            :rowData="rowData"
            :rowSelection="rowSelection"
            :rowHeight="50"
          >
          </ag-grid-vue>
        </v-col>
      </v-row>
    </v-container>
  </v-app>
</template>

<script lang="ts">
import axios from "axios";
import "ag-grid-community/styles/ag-grid.css";
import "ag-grid-community/styles/ag-theme-alpine.css";
import { AgGridVue } from "ag-grid-vue3";
import Toolbar from "@/components/Toolbar.vue";
export default {
  components: {
    AgGridVue,
    Toolbar,
  },
  data() {
    return {
      columnDefs: [
        {
          headerName: "Rank",
          field: "rank",
          cellStyle: {
            textAlign: "center",
          },
        },
        {
          headerName: "Name",
          field: "id",
          cellStyle: {
            textAlign: "center",
          },
        },
        { headerName: "Change (24Hr)", field: "changePercent24Hr" },
        { headerName: "Price", field: "priceUsd" },
        { headerName: "Market Cap", field: "marketCapUsd" },
        { headerName: "VWAP (24Hr)", field: "vwap24Hr" },
        { headerName: "Supply", field: "supply" },
        { headerName: "Volume (24Hr)", field: "volumeUsd24Hr" },
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
      console.log("çalıştı");
      this.rowData.data.map((x: any) => {
        Number(x.marketCapUsd);
        x.marketCapUsd = this.getNumberUnit(x.marketCapUsd);
      });
    },
    getNumberUnit(num: any) {
      var units = ["M","B","T","Q"]
    var unit = Math.floor((num / 1.0e+1).toFixed(0).toString().length)
    var r = unit%3
    var x =  Math.abs(Number(num))/(Number('1.0e+'+(unit-r)).toFixed(2))
    return x.toFixed(2)+ ' ' + units[Math.floor(unit / 3) - 2]

    },
  },
  mounted() {
    axios
      .get("https://api.coincap.io/v2/assets")
      .then((res: any) => {
        console.log(res.data.data);
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
  --ag-header-cell-hover-background-color: rgb(70, 0, 91);
  --ag-header-cell-moving-background-color: rgb(255, 255, 255);
  --ag-selected-row-background-color: rgba(159, 142, 255, 0.603);
}
</style>
