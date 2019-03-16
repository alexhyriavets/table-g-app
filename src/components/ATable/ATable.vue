<template>
  <div class="a-table">
    <header class="a-table__header">
      <ASearch v-model="searchString" />
    </header>
    <div class="a-table__content">
      <table class="a-table__table">
        <thead>
          <tr>
            <th
              v-for="(title, titleKey) in data.titles"
              :key="`title-${titleKey}`"
              :class="['a-table__th', { [sortClass]: sortBy === titleKey }]"
              @click="changeSorting(titleKey)"
            >
              {{ title }}
            </th>
          </tr>
        </thead>
        <tbody>
          <tr
            v-for="(row, index) in currentPageItems"
            :key="`row-${index}`"
            class="a-table__body-tr"
          >
            <td
              v-for="(item, itemIndex) in row"
              :key="`item-${itemIndex}`"
              class="a-table__td"
            >
              {{ item }}
            </td>
          </tr>
        </tbody>
      </table>
    </div>
    <footer class="a-table__footer">
      <APagination
        v-model="activePage"
        :total-items="totalItems"
        :per-page="perPage"
      />
    </footer>
  </div>
</template>

<script>
import ASearch from './ASearch.vue'
import APagination from './APagination.vue'

import {
  has,
  both,
  filter,
  compose,
  includes,
  toLower,
  values,
  join,
  descend,
  ascend,
  sort,
  prop,
  slice
} from 'ramda'

const includesSearchText = searchText =>
  compose(
    includes(toLower(searchText)),
    toLower,
    join('~'),
    values
  )

export default {
  name: 'ATable',
  components: {
    ASearch,
    APagination
  },
  props: {
    data: {
      type: Object,
      required: true,
      validator: both(has('titles'), has('items'))
    },
    perPage: {
      type: Number,
      default: 5
    }
  },
  data() {
    return {
      searchString: '',
      activePage: 1,
      sortBy: 'name',
      isAscendSort: true
    }
  },
  computed: {
    filteredItems() {
      const sortByFn = compose(
        this.sortFn,
        prop
      )

      const filterAndSortItems = compose(
        sort(sortByFn(this.sortBy)),
        filter(includesSearchText(this.searchString)),
        prop('items')
      )

      return filterAndSortItems(this.data)
    },
    currentPageItems() {
      const from = (this.activePage - 1) * this.perPage
      const to = this.activePage * this.perPage

      return slice(from, to, this.filteredItems)
    },
    totalItems() {
      return this.filteredItems.length
    },
    sortClass() {
      return this.isAscendSort ? 'a-table__th--ascend' : 'a-table__th--descend'
    },
    sortFn() {
      return this.isAscendSort ? ascend : descend
    }
  },
  methods: {
    changeSorting(titleKey) {
      this.sortBy = titleKey
      this.isAscendSort = !this.isAscendSort
    }
  }
}
</script>

<style lang="scss">
.a-table {
  &__header {
    display: flex;
    align-items: center;
    width: 100%;
    padding: 30px;
  }

  &__footer {
    display: flex;
    align-items: center;
    justify-content: center;
    padding: 30px 0;
  }

  &__table {
    border-collapse: collapse;
    width: 100%;
    margin-bottom: 5px;
  }

  &__body-tr {
    &:nth-child(odd) {
      background-color: #f9f9f9;
    }
  }

  &__th {
    text-align: left;
    border: 1px solid #ddd;
    border-bottom: 3px solid #ddd;
    font-family: 'Glyphicons Halflings';
    position: relative;
    cursor: pointer;

    &::after {
      content: '';
      position: absolute;
      top: 12px;
      right: 8px;
      display: block;
      opacity: 0.2;
      font-size: 0.7em;
    }

    &--ascend {
      &::after {
        content: '\25b2';
        opacity: 0.8;
      }
    }

    &--descend {
      &::after {
        content: '\25bc';
        opacity: 0.8;
      }
    }
  }

  &__td {
    border: 1px solid #ddd;
  }

  &__td,
  &__th {
    padding: 8px;
  }
}
</style>
