<template>
  <v-app>
    <v-app-bar style="position: fixed" flat height="100%">
        
      <Toolbar /> 
     
    </v-app-bar>

    <v-container>
      <v-row>
        <v-col cols="12"> </v-col>
      </v-row>
    </v-container>
  </v-app>
</template>

<script lang="ts">
import axios from "axios";
import "ag-grid-community/styles/ag-grid.css";
import "ag-grid-community/styles/ag-theme-alpine.css";
import { AgGridVue } from "ag-grid-vue3";
import Toolbar from '@/components/Toolbar.vue';
export default {
  components: {
    AgGridVue,
    Toolbar,
  },
  data() {
    return {
      columnDefs: [
        { headerName: "area", field: "area" },
        { headerName: "Common", field: "name.common" },
        { headerName: "Region", field: "region" },
      ] as any,
      rowData: [] as any,
    };
  },
  mounted() {
    axios
      .get("https://api.coincap.io/v2/assets/bitcoin/markets")
      .then((res: any) => console.log(res.data))
      .catch((e: any) => console.log("err"));
  },
};
</script>
