<template>
  <div id="app">
    <ATable :data="$options.tableData">
      <template v-slot:actions="{ row }">
        <button @click="editRow(row)" style="margin-left: 12px;">Edit</button>
        <button @click="deleteRow(row)" style="margin-left: 12px;">
          Delete
        </button>
      </template>
    </ATable>
  </div>
</template>

<script>
import tableData from './data.json'

import ATable from '@/components/ATable'

export default {
  tableData,

  name: 'app',
  components: {
    ATable
  },
  methods: {
    editRow(row) {
      console.log(row)
    },
    deleteRow(row) {
      console.log(row)
    }
  }
}
</script>

<style lang="scss">
body {
  margin: 0;
  padding: 0;
}
</style>
