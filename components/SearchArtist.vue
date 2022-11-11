<template>
  <div class="container-fluid">
    <form
      class="row row-cols-lg-auto g-3 align-items-center"
      @submit.prevent="getArtist()"
    >
      <div class="d-flex justify-content-between col-12 mt-4">
        <div class="col-2">
          <input
            type="text"
            class="form-control"
            id="colFormLabel"
            v-model="artist"
            placeholder="Search item"
          />
        </div>
        <div class="col-12">
          <button type="submit" class="btn btn-primary">Search</button>
        </div>
      </div>
    </form>

    <ul class="list-group col-2" v-if="artists">
      <li
        class="list-group-item btn btn-light"
        v-for="artist in artists"
        :key="artist.id"
      >
        <nuxt-link :to="'/profile/' + artist.id" target="_blank">{{ artist.name }}</nuxt-link>
      </li>
    </ul>

    <button
      type="button"
      class="btn btn-secondary btn-sm"
      v-if="previous"
      v-on:click="getPrevious()"
    >
      &laquo;
    </button>
    <button
      type="button"
      class="btn btn-secondary btn-sm"
      v-if="next"
      v-on:click="getNext()"
    >
      &raquo;
    </button>
  </div>
</template>

<script>
import axios from 'axios'
import oauth from 'axios-oauth-client'

let options
export default {
  name: 'SearchArtist',
  data() {
    return {
      artist: '',
      artists: [],
      previous: '',
      next: '',
      token: '',
    }
  },
  mounted() {
    this.getToken()
  },
  methods: {
    async getToken() {
      const getClientCredentials = oauth.clientCredentials(
        axios.create(),
        'https://accounts.spotify.com/api/token',
        '7372f2ca7c854d60a5c0b746b69db9b9',
        'b6c23e8d419b432fb6616988a1d80992'
      )
      const auth = await getClientCredentials()
      this.token = auth.access_token
    },
    async getArtist() {
      if (this.artist == '') {
        return
      }
      options = {
        method: 'GET',
        url:
          'https://api.spotify.com/v1/search?q=' + this.artist + '&type=artist',
        headers: {
          Authorization: 'Bearer ' + this.token,
        },
      }
      await axios
        .request(options)
        .then((response) => {
          this.artists = response.data.artists.items
          this.next = response.data.artists.next
          this.previous = response.data.artists.previous
        })
        .catch((error) => {
          console.error(error)
        })
    },
    async getNext() {
      options = {
        method: 'GET',
        url: this.next,
        headers: {
          Authorization: 'Bearer ' + this.token,
        },
      }
      await axios
        .request(options)
        .then((response) => {
          this.artists = response.data.artists.items
          this.next = response.data.artists.next
          this.previous = response.data.artists.previous
        })
        .catch((error) => {
          console.error(error)
        })
    },
    async getPrevious() {
      options = {
        method: 'GET',
        url: this.previous,
        headers: {
          Authorization: 'Bearer ' + this.token,
        },
      }
      await axios
        .request(options)
        .then((response) => {
          this.artists = response.data.artists.items
          this.next = response.data.artists.next
          this.previous = response.data.artists.previous
        })
        .catch((error) => {
          console.error(error)
        })
    },
  },
}
</script>
