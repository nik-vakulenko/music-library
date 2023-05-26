<template>
    <SortTable
      :list="pageList"
      :totalPages="10"
      :perPage="perPage"
      :currentPage="currentPage"
      :sortField="sortField"
      :sortDirection="sortDirection"
      @pagechanged="onPageChange"
      @sortchanged="onSortChange"
    />
</template>
<script>
import axios from 'axios'
import {toRaw} from "vue";
import SortTable from "./components/SortTable.vue";
export default {
  components: {SortTable},
  data () {
    return {
      currentPage: 1,
      perPage: 10,
      sortDirection: 'up',
      sortField: 'artistName',
      albums: [],
      artists: [],
      songs: [],
      list: [],
      token: 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c',
    }
  },
  computed: {
    pageList() {
      return this.list.slice((this.currentPage - 1) * this.perPage,this.currentPage * this.perPage)
    }
  },
  methods: {
    onPageChange(page) {
      this.currentPage = page;
    },
    onSortChange(field) {
      if (this.sortField === field)
        this.sortDirection = (this.sortDirection === 'up') ? 'down' : 'up'
      else this.sortField = field
      this.reSort()
    },
    reSort() {
      if (this.sortDirection === 'up')
        this.list.sort((x, y) => (x[this.sortField] > y[this.sortField] ? -1 : 1));
      else
        this.list.sort((x, y) => (x[this.sortField] < y[this.sortField] ? -1 : 1));
    },
    combine() {
      this.songs.forEach(song => {
        const albums = toRaw(this.albums);
        const artists = toRaw(this.artists);
        const album = albums.find(album => { return album.id === song.album_id })
        const artist = artists.find(art => art.id == album.artist_id )
        this.list.push({
          id: song.id,
          artistName: artist.name,
          albumName : album.name,
          yearReleased : album.year_released,
          songTrack : song.track,
          songName: song.name,
        })
        this.reSort()
      })
    },
  },
  mounted() {
    let album = axios.get('http://localhost:5000/albums', {
      headers: { Authorization: `Bearer ${this.token}` }
    })
        .then((response) => {
          this.albums = response.data
        })
    let artist =  axios.get('http://localhost:5000/artists', {
      headers: { Authorization: `Bearer ${this.token}` }
    })
        .then((response) => {
          this.artists = response.data
        })
    let songs = axios.get('http://localhost:5000/songs', {
      headers: { Authorization: `Bearer ${this.token}` }
    })
        .then((response) => {
          this.songs = response.data
        })
    Promise.all([album, artist, songs]).then( () => {
      this.combine()
    })
    //this.combine()
    this.dataReady = true;
  }


}
</script>
<style scoped>
</style>
