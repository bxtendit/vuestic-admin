<template>
  <va-card :title="$t('tables.searchTrendsBadges')">
    <div class="row align--center">
      <div class="flex xs12 md6">
        <va-input
          :value="term"
          :placeholder="$t('tables.searchByName')"
          @input="search"
          removable
        >
          <va-icon name="fa fa-search" slot="prepend" />
        </va-input>
      </div>

      <div class="flex xs12 md3 offset--md3">
        <va-select
          v-model="perPage"
          :label="$t('tables.perPage')"
          :options="perPageOptions"
          noClear
        />
      </div>
    </div>

    <va-data-table
      :fields="fields"
      :data="filteredData"
      :per-page="parseInt(perPage)"
      @row-clicked="showUser"
      clickable
    >
      <template slot="trend" slot-scope="props">
        <va-icon :name="getTrendIcon(props.rowData)" :color="getTrendColor(props.rowData)" />
      </template>

      <template slot="artist" slot-scope="props">
        <div>{{ getArtistNames(props.rowData.artist) }}</div>
      </template>

      <template slot="tracks" slot-scope="props">
        <div>{{ getTrackNames(props.rowData.tracks) }}</div>
      </template>

      <template slot="status" slot-scope="props">
        <va-badge :color="props.rowData.color">
          {{ props.rowData.status }}
        </va-badge>
      </template>

      <template slot="actions" slot-scope="props">
        <va-button v-if="props.rowData.hasReport" small color="danger" class="ma-0">
          {{ $t('tables.report') }}
        </va-button>
      </template>
      <template slot="coverart" slot-scope="props">
        <img :src="getCoverArt(props.rowData.coverArt)" class="data-table-server-pagination---avatar">
      </template>
    </va-data-table>
  </va-card>
</template>

<script>
import { debounce } from 'lodash'
import users from '../../../data/users.json'
import gql from 'graphql-tag'

export default {
  data () {
    return {
      term: null,
      perPage: '6',
      perPageOptions: ['4', '6', '10', '20'],
      users: users,
    }
  },
  apollo: {
    // Simple query that will update the 'hello' vue property
    albums: gql`query {
      albums {
        id
        name
        artist {
          name
        }
        type
        price
        tracks {
          name
          duration
          price
        }
        coverArt {
          url
          fileName
        }
      }
    }`,
  },
  computed: {
    fields () {
      // console.log(this.albums.artist)
      // console.log(this.albums)
      return [{
        name: '__slot:trend',
        width: '30px',
        height: '45px',
        dataClass: 'text-center',
      }, {
        name: '__slot:coverart',
        width: '60px',
      }, {
        name: 'name',
        title: 'Album', // this.$t('tables.headings.name'),
        width: '20%',
      }, {
        name: '__slot:artist', // '__slot:status',
        title: 'Artists', // this.$t('tables.headings.status'),
        width: '20%',
      }, {
        name: 'type',
        title: 'Type', // this.$t('tables.headings.email'),
        width: '10%',
      }, {
        name: 'price',
        title: 'Price', // this.$t('tables.headings.email'),
        width: '10',
      }, {
        name: '__slot:tracks',
        title: 'Tracks', // this.$t('tables.headings.email'),
        width: '25%',
      }, {
        name: '__slot:actions',
        dataClass: 'text-right',
      }]
    },
    filteredData () {
      if (!this.term || this.term.length < 1) {
        return this.albums // return this.users
      }

      return this.albums.filter(item => {
        return item.name.toLowerCase().startsWith(this.term.toLowerCase())
        // return item.fullName.toLowerCase().startsWith(this.term.toLowerCase())
      })
    },
  },
  methods: {
    getArtistNames: (artists) => {
      artists = artists.map(artist => artist.name)
      // const iterator = artists.values()
      // for (const value of iterator) {
      //   console.log(value)
      // }
      artists = artists.toString()
      return artists
    },
    getTrackNames: (tracks) => {
      tracks = tracks.map(track => track.name)
      // const iterator = tracks.values()
      // for (const value of iterator) {
      //   console.log(value)
      // }
      tracks = tracks.join(' - ')
      return tracks
    },
    getCoverArt: (art) => {
      art = art.map(art => art.url)
      // const iterator = tracks.values()
      // for (const value of iterator) {
      console.log(art)
      // }
      // art = art.join(' - ')
      return art
    },
    getTrendIcon (user) {
      if (user.trend === 'up') {
        return 'fa fa-caret-up'
      }

      if (user.trend === 'down') {
        return 'fa fa-caret-down'
      }

      return 'fa fa-minus'
    },
    getTrendColor (user) {
      if (user.trend === 'up') {
        return 'primary'
      }

      if (user.trend === 'down') {
        return 'danger'
      }

      return 'grey'
    },
    showUser (album) {
      alert(JSON.stringify(album))
    },
    search: debounce(function (term) {
      this.term = term
    }, 400),
  },
}
</script>
