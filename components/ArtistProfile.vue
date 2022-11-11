<template>
  <div class="card m-3" style="width: 20rem">
    <img
      v-bind:src="images"
      class="img-fluid img-thumbnail card-img-top"
      alt="profile-icon"
    />
    <div class="card-body">
      <h5 class="card-title">{{name}}</h5>
      <div class="card-text">
        <a :href="external">{{external}}</a>
        <h6>Popularity: {{popularity}}</h6>
        <h6>Followers: {{followers}}</h6>
        <h6>Genres: {{genres}}</h6>
      </div>
    </div>
  </div>
</template>

<script>
import VueRouter from 'vue-router';
import axios from 'axios';
import oauth from 'axios-oauth-client';

let options;
export default {
  name: 'ArtistProfile',
  data() {
    return {
      artist: '',
      artistID: '',
      images: '',
      name: '',
      popularity: '',
      followers: '',
      genres: [],
      external: '',
      token:'',
    }
  },
  mounted() {
    this.getId();
    this.getToken();
  },
  methods: {
    async getToken() {
      const getClientCredentials = oauth.clientCredentials(
        axios.create(),
        'https://accounts.spotify.com/api/token',
        '7372f2ca7c854d60a5c0b746b69db9b9',
        'b6c23e8d419b432fb6616988a1d80992'
      )
      const auth = await getClientCredentials();
      this.token = auth.access_token;
      this.getArtist();
    },
    async getId() {
      this.artistID = this.$route.params.view;
      this.getToken();
    },
    async getArtist() {
      options = {
        method: 'GET',
        url:
          'https://api.spotify.com/v1/artists/'+ this.artistID,
        headers: {
          Authorization:
          'Bearer ' + this.token,
        },
      }
      await axios
        .request(options)
        .then((response) => {
          this.artist = response.data;
          this.name = this.artist.name;
          this.external = this.artist.external_urls.spotify;
          this.popularity = this.artist.popularity;
          this.followers = this.artist.followers.total;
          this.genres = this.artist.genres.toString();
          this.images = this.artist.images[0].url;
        })
        .catch((error) => {
          console.error(error)
        })
    },
  },
}
</script>
