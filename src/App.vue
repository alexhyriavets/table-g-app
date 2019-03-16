<template>
  <div id="app">
    <ATable :data="$options.tableData" />
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
  }
}
</script>

<style lang="scss">
body {
  margin: 0;
  padding: 0;
}
</style>
