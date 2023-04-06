<template>
  <v-app>
    <v-app-bar style="position: fixed" flat height="100%">
      <Toolbar />
    </v-app-bar>
    <v-img
      src="https://cdn.pixabay.com/photo/2016/11/10/05/09/bitcoin-1813503__480.jpg"
      cover
    >
      <v-container class="mt-16">
        <v-btn @click="inter">ss</v-btn>
        <v-row class="mt-16">
          <v-col cols="11">
            <ag-grid-vue
              style="height: 700px; width: 100%"
              class="ag-theme-alpine-dark font-weight-thin text-caption --ag-borders-none"
              :columnDefs="columnDefs"
              :defaultColDef="defaultColDef"
              :rowData="rowData"
              rowHeight="40"
              
            >
            </ag-grid-vue>
          </v-col>
        </v-row>
      </v-container>
    </v-img>
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
        { headerName: "Rank", field: "rank" },
        { headerName: "Name", field: "id" },
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
    };
  },
  methods: {
    inter() {
    },
  },
  mounted() {
    axios
      .get("https://api.coincap.io/v2/assets")
      .then((res: any) => {
        console.log(res.data.data);
        this.rowData = res.data.data;
      })
      .catch((e: any) => console.log("err"));
  },
};
</script>
<style scoped>
.ag-theme-alpine-dark{
  --ag-borders: none;
  --ag-header-height: 30px;
    --ag-header-foreground-color: white;
    --ag-header-background-color: rgba(4, 4, 4, 0);
    --ag-header-cell-hover-background-color: rgb(80, 40, 140);
    --ag-header-cell-moving-background-color: rgb(80, 40, 140);
}
</style>
