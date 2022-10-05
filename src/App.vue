<template>
  <v-app>
    <v-main>
      <Table :tableData="this.tableData" />
    </v-main>
  </v-app>
</template>

<script>
import Table from "./components/Table.vue";
import tableData from "../table-data.json";
export default {
  name: "App",

  components: {
    Table,
  },

  data: () => ({
    tableData: tableData,
  }),
};
</script>
