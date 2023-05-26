<template>
  <table>
    <tr>
      <th @click="onSort('artistName')">Artist Name <i v-if="sortField==='artistName'" :class="sortDirection === 'up' ? 'up' : 'down'"></i></th>
      <th @click="onSort('albumName')">Album Name <i v-if="sortField==='albumName'" :class="sortDirection === 'up' ? 'up' : 'down'"></i></th>
      <th @click="onSort('yearReleased')">Year Released <i v-if="sortField==='yearReleased'" :class="sortDirection === 'up' ? 'up' : 'down'"></i></th>
      <th @click="onSort('songTrack')">Song Track #<i v-if="sortField==='songTrack'" :class="sortDirection === 'up' ? 'up' : 'down'"></i></th>
      <th @click="onSort('songName')">Song Name<i v-if="sortField==='songName'" :class="sortDirection === 'up' ? 'up' : 'down'"></i></th>
    </tr>
    <tr v-for="song in list">
      <td>{{song.artistName}}</td>
      <td>{{song.albumName}}</td>
      <td>{{song.yearReleased}}</td>
      <td>{{song.songTrack}}</td>
      <td>{{song.songName}}</td>
    </tr>
  </table>
  <ul class="pagination">
    <li class="pagination-item">
      <button
          type="button"
          @click="onClickFirstPage"
          :disabled="isInFirstPage"
      >
        First
      </button>
    </li>

    <li class="pagination-item">
      <button
          type="button"
          @click="onClickPreviousPage"
          :disabled="isInFirstPage"
      >
        Previous
      </button>
    </li>

    <!-- Visible Buttons Start -->

    <li
        v-for="page in pages"
        class="pagination-item"
    >
      <button
          type="button"
          @click="onClickPage(page.name)"
          :disabled="page.isDisabled"
          :class="{ active: isPageActive(page.name) }"
      >
        {{ page.name }}
      </button>
    </li>

    <!-- Visible Buttons End -->

    <li class="pagination-item">
      <button
          type="button"
          @click="onClickNextPage"
          :disabled="isInLastPage"
      >
        Next
      </button>
    </li>

    <li class="pagination-item">
      <button
          type="button"
          @click="onClickLastPage"
          :disabled="isInLastPage"
      >
        Last
      </button>
    </li>
  </ul>
</template>

<script>
export default {
props: {
  sortField: {
    type: String,
    required: true,
  },
  sortDirection: {
    type: String,
    required: true,
  },
  maxVisibleButtons: {
    type: Number,
    required: false,
    default: 3
  },
  list: {
    type: Array,
    required: true
  },
  totalPages: {
    type: Number,
    required: true
  },
  perPage: {
    type: Number,
    required: true
  },
  currentPage: {
    type: Number,
    required: true
  },
},
  emits: ["pagechanged", "sortchanged"],
  computed: {
    startPage() {
      if (this.currentPage === 1) {
        return 1;
      }

      if (this.currentPage === this.totalPages) {
        return this.totalPages - this.maxVisibleButtons + 1;
      }

      return this.currentPage - 1;

    },
    endPage() {

      return Math.min(this.startPage + this.maxVisibleButtons - 1, this.totalPages);

    },
    pages() {
      const range = [];

      for (let i = this.startPage; i <= this.endPage; i+= 1 ) {
        range.push({
          name: i,
          isDisabled: i === this.currentPage
        });
      }

      return range;
    },
    isInFirstPage() {
      return this.currentPage === 1;
    },
    isInLastPage() {
      return this.currentPage === this.totalPages;
    },
  },
  methods: {
    onSort(param) {
      this.$emit('sortchanged', param)
    },
    onClickFirstPage() {
      this.$emit('pagechanged', 1);
    },
    onClickPreviousPage() {
      this.$emit('pagechanged', this.currentPage - 1);
    },
    onClickPage(page) {
      this.$emit('pagechanged', page);
    },
    onClickNextPage() {
      this.$emit('pagechanged', this.currentPage + 1);
    },
    onClickLastPage() {
      this.$emit('pagechanged', this.totalPages);
    },
    isPageActive(page) {
      return this.currentPage === page;
    },
  }
}
</script>
<style scoped lang="scss">
  table {
    width: 100%;
    border-spacing: 0;
  }
  .pagination {
    list-style-type: none;
  }

  .pagination-item {
    display: inline-block;
  }

  .active {
    background-color: #4AAE9B;
    color: #ffffff;
  }

  th, td {
    text-align: center;
    width: 20%;
    border: 1px solid #d9d9d9;
  }
  th {
    background-color: #4AAE9B;
    color: #fff;
    cursor: pointer;
  }
  i.up:before {
    content: "↑"
  }
  i.down:before {
    content: "↓"
  }
</style>
