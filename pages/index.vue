<template>
  <div>
    <div class="d-flex justify-content-center">
      <b-col cols="8" id="headerApp">
        <b-row class="justify-content-center">
          <img src="../static/popcorn.png" alt="" width="100" />
          <h1 class="align-self-center" id="titleApp">What2Watch</h1>
        </b-row>
        <p class="text-center" id="sloganApp">
          Qu'est ce qu'on regarde ce soir ?
        </p>
      </b-col>
    </div>
    <div class="d-flex justify-content-center">
      <b-col cols="6" id="search-box">
        <b-row class="row-style">
          <label class="label-search" for="genre-select"><i class="fas fa-arrow-circle-right"></i> Quel genre de film ?</label>
          <b-form-select v-model="genre_selected" name="genre-select" class="mb-3 input-form">
            <template #first>
              <b-form-select-option :value="null" disabled>-- Sélectionner un genre --</b-form-select-option>
            </template>
            <b-form-select-option :value="genre.id" v-for="genre in genres.genres" v-bind:key="genre.id">{{ genre.name }}</b-form-select-option>
          </b-form-select>
        </b-row>

        <b-row class="row-style">
          <label class="label-search" for="date-start-select"><i class="fas fa-arrow-circle-right"></i> Film sorti entre </label>
          <b-form-datepicker id="date-start-select" v-model="value_date_start" class="mb-2 input-form-date" placeholder="-- Date --"></b-form-datepicker>
          <label class="label-search" for="date-end-select" style="margin-left: 15px">et</label>
          <b-form-datepicker id="date-end-select" v-model="value_date_end" class="mb-2 input-form-date" placeholder="-- Date --"></b-form-datepicker>
        </b-row>

        <b-row class="row-style">
          <label class="label-search" for="platform-select"><i class="fas fa-arrow-circle-right"></i> Quelle plateforme ?</label>
          <b-form-select v-model="platform_selected" name="platform-select" class="mb-3 input-form">
            <template #first>
              <b-form-select-option :value="null" disabled>-- Sélectionner une plateforme --</b-form-select-option>
            </template>
            <b-form-select-option :value="platform.provider_id" v-for="platform in platforms.results" v-bind:key="platform.provider_id">{{ platform.provider_name }}</b-form-select-option>
          </b-form-select>
        </b-row>
        <button @click="searchMovie">test</button>
      </b-col>
    </div>
    <div v-if="show">
      <div class="d-flex justify-content-center">
      <b-col cols="6" id="search-box" v-for="movie in movies" v-bind:key="movie.id">
        <h1>{{ movie.titre }}</h1>
      </b-col>
    </div>
    </div>

  </div>
</template>

<script>
export default {
  async fetch() {
    (this.genres = await fetch(
      "https://api.themoviedb.org/3/genre/movie/list?api_key=d26ad6e614a59a93420fc409d7922d8d&language=fr-FR"
    ).then((res) => {
      if (res.ok) {
        return res.json();
      }
      throw new Error(res.status); // Traitement des erreurs
    })),
      (this.platforms = await fetch(
        "https://api.themoviedb.org/3/watch/providers/movie?api_key=d26ad6e614a59a93420fc409d7922d8d&language=fr-FR&watch_region=FR"
      ).then((res) => {
        if (res.ok) {
          return res.json();
        }
        throw new Error(res.status); // Traitement des erreurs
      }))
      ;
  },
  data() {
    return {
      show:false,
      genre_selected: null,
      platform_selected: null,
      value_date_start: null,
      value_date_end: null,
      genres: [],
      platforms: [],
      movies: [],
    };
  },
  mounted() {

  },
  methods: {
     searchMovie() {
       this.movies = [];
        fetch(
        `https://api.themoviedb.org/3/discover/movie?api_key=d26ad6e614a59a93420fc409d7922d8d&language=fr-FR&sort_by=original_title.asc&release_date.gte=${this.value_date_start}&release_date.lte=${this.value_date_end}&with_genres=${this.genre_selected}&with_watch_providers=${this.platform_selected}&watch_region=FR`)
        .then((res) => res.json())
        .then((films) => {
          let x = Math.floor(Math.random() * parseInt(films.total_pages) + 1);
          fetch(
            `https://api.themoviedb.org/3/discover/movie?api_key=d26ad6e614a59a93420fc409d7922d8d&language=fr-FR&sort_by=original_title.asc&page=${x}&release_date.gte=${this.value_date_start}&release_date.lte=${this.value_date_end}&with_genres=${this.genre_selected}&with_watch_providers=${this.platform_selected}&watch_region=FR`
          ).then((res) => res.json())
          .then((films)=>{
            let x = Math.floor(Math.random() * parseInt(20));
            this.movies.push({
              id: films.results[x].id,
              titre: films.results[x].title
            })
            this.show = true;
          })
        });
    },
  },
};
</script>

<style>
body {
  background-color: #cf3333;
  color: #ffff;
}

#titleApp {
  font-family: Balsamiq Sans;
  text-shadow: 1px 1px 2px black;
  margin-bottom: -1rem;
  margin-left: 1rem;
  font-size: 55px;
}

#headerApp {
  margin-top: 15px;
}

#sloganApp {
  font-family: Balsamiq Sans;
  text-shadow: 1px 1px 2px black;
  margin-top: -5px;
  font-size: 30px;
}

#search-box {
  margin-top: 2rem;
  background-color: #e07878;
  border-radius: 10px;
  padding: 20px;
  box-shadow: rgba(0, 0, 0, 0.6) 0px 2px 4px 0px inset;
}

#search-box .row-style {
  margin-left: 0 !important;
  margin-right: 0;
}

.input-form {
  width: 400px;
  margin-left: 15px;
}

.input-form-date {
  width: 204px;
  margin-left: 15px;
}

.label-search {
  font-size: 26px;
}

/* https://api.themoviedb.org/3/watch/providers/movie?api_key=d26ad6e614a59a93420fc409d7922d8d&language=fr-FR&watch_region=FR */
</style>
