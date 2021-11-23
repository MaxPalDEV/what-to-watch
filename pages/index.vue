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
        <div class="text-center button-box d-flex" v-if="show">
          <button id="newsearch-button" class="button-custom d-flex" @click="newSearch"><span class="logo-button"><i class="fas fa-search wobble-hor-bottom"></i>&nbsp;</span><span class="align-self-center ml-2"> Nouvelle Recherche</span></button>  <button id="refresh-button" class="button-custom d-flex" @click="searchMovie"><span class="logo-button"><i class="fas fa-sync-alt rotate-scale-up"></i>&nbsp;</span><span class="align-self-center ml-2">Autre Proposition</span></button>
        </div>

      <div id="search-box" v-if="!show">
        <b-alert v-if="error" show variant="danger">{{ error_message }}</b-alert>
        <b-row>
      <b-col cols="7" class="filter-box">
           <b-col>
          <label class="label-search" for="genre-select"><i class="fas fa-arrow-circle-right icon-search"></i> Quel genre de film ?</label>
        </b-col>
        <b-col class="ml-4">
          <b-form-select v-model="genre_selected" name="genre-select" class="mb-3 input-form">
            <template #first>
              <b-form-select-option :value="null" disabled>-- Sélectionner un genre --</b-form-select-option>
            </template>
            <b-form-select-option :value="genre.id" v-for="genre in genres.genres" v-bind:key="genre.id">{{ genre.name }}</b-form-select-option>
          </b-form-select>
        </b-col>
        <b-col class="mt-1">
          <p class="label-search"><i class="fas fa-arrow-circle-right icon-search"></i> Période de sortie : </p>
        </b-col>
        <b-col >
          <b-row class="ml-4" style="flex-wrap: nowrap;">
            <b-form-select v-model="value_date_start" name="genre-select" class="mb-3 input-form-date">
            <template #first>
              <b-form-select-option :value="null" disabled>-- Année --</b-form-select-option>
            </template>
            <b-form-select-option :value="year" v-for="year in years.slice().reverse()" v-bind:key="year">{{ year }}</b-form-select-option>
          </b-form-select>
          <span class="label-search" style="margin-left: 15px">-</span>
          <b-form-select v-model="value_date_end" name="genre-select" class="mb-3 input-form-date">
            <template #first>
              <b-form-select-option :value="null" disabled>-- Année --</b-form-select-option>
            </template>
            <b-form-select-option :value="year" v-for="year in years.slice().reverse()" v-bind:key="year">{{ year }}</b-form-select-option>
          </b-form-select>
          </b-row>
        </b-col>
        <b-col class="mt-1">
          <p class="label-search"><i class="fas fa-arrow-circle-right icon-search"></i> Quelle plateforme ? </p>
        </b-col>
        <b-col class="ml-4">
          <b-form-select v-model="platform_selected" name="platform-select" class="mb-3 input-form">
            <template #first>
              <b-form-select-option :value="null" disabled>-- Sélectionner une plateforme --</b-form-select-option>
            </template>
            <b-form-select-option :value="platform.provider_id" v-for="platform in platforms.results" v-bind:key="platform.provider_id">{{ platform.provider_name }}</b-form-select-option>
          </b-form-select>
        </b-col>
        </b-col>
        <b-col cols="4" class="playBox">
          <div id="wrapper">
  <a class="my-super-cool-btn">
    <div class="dots-container">
      <div class="dot"></div>
      <div class="dot"></div>
      <div class="dot"></div>
      <div class="dot"></div>
    </div>
    <span @click="searchMovie" class="clapboard">
    </span>
  </a>
</div>

          </b-col>

        </b-row>
</div>
    </div>

    <div v-if="show">
      <Movie :movies="movies" :poster="poster" />
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
      error:false,
      error_message:'',
      show:false,
      genre_selected: null,
      platform_selected: null,
      value_date_start: null,
      value_date_end: null,
      poster:null,
      genres: [],
      platforms: [],
      movies: [],
    };
  },
  mounted() {

  },
  methods: {
     searchMovie() {
       if (this.genre_selected == null || this.value_date_start == null || this.value_date_end == null || this.platform_selected == null) {
         this.error = true
         this.error_message = "Veuillez renseigner tous les champs !"
       }
       else if(this.value_date_start > this.value_date_end){
         this.error = true
         this.error_message = "La date de début est supérieure à la date de fin !"
       }
       else {
         this.error = false;
         this.movies = [];
        fetch(
        `https://api.themoviedb.org/3/discover/movie?api_key=d26ad6e614a59a93420fc409d7922d8d&language=fr-FR&sort_by=original_title.asc&primary_release_date.gte=${this.value_date_start}-01-01&primary_release_date.lte=${this.value_date_end}-12-31&with_genres=${this.genre_selected}&with_watch_providers=${this.platform_selected}&watch_region=FR`)
        .then((res) => res.json())
        .then((films) => {
          let x = Math.floor(Math.random() * parseInt(films.total_pages) + 1);
          fetch(
            `https://api.themoviedb.org/3/discover/movie?api_key=d26ad6e614a59a93420fc409d7922d8d&language=fr-FR&sort_by=original_title.asc&page=${x}&primary_release_date.gte=${this.value_date_start}-01-01&primary_release_date.lte=${this.value_date_end}-12-31&with_genres=${this.genre_selected}&with_watch_providers=${this.platform_selected}&watch_region=FR`
          ).then((res) => res.json())
          .then((films)=>{
            let x = Math.floor(Math.random() * parseInt(films.results.length));
            /*
            this.poster= "https://image.tmdb.org/t/p/original" + this.movies[0]*/


            fetch(`https://api.themoviedb.org/3/movie/${films.results[x].id}?api_key=d26ad6e614a59a93420fc409d7922d8d&language=fr-FR`)
            .then((res) => res.json())
            .then((film) => {
              this.movies.push(
              film
            )

            this.poster= "https://image.tmdb.org/t/p/original" + film.poster_path


            this.show = true;
            })
          })
        });
       }
    },
    newSearch(){
      this.show = false;
    },
  },
  computed : {
    years () {
      const year = new Date().getFullYear()
      return Array.from({length: year - 1969}, (value, index) => 1970 + index)
    },
  }
};
</script>

<style>
/* STYLE DU BOUTON SEARCH */
@import '~/assets/style/search_button.css';

body {
  background: linear-gradient(to bottom,rgba(246,66,66,.8) 0,rgba(246,66,66,.8) 100%),url(../static/bgwhattowatch.jpg); /*#cf3333;*/
  background-position: center;
  background-repeat: no-repeat;
  background-attachment: fixed;
  background-size: cover;
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
  width: 700px!important;
  /*box-shadow: rgba(0, 0, 0, 0.6) 0px 2px 4px 0px inset;*/
  box-shadow: rgba(0, 0, 0, 0.25) 0px 54px 55px, rgba(0, 0, 0, 0.12) 0px -12px 30px, rgba(0, 0, 0, 0.12) 0px 4px 6px, rgba(0, 0, 0, 0.17) 0px 12px 13px, rgba(0, 0, 0, 0.09) 0px -3px 5px;
  transition: max-width 0.5s;
}

#search-box .row-style {
  margin-left: 0 !important;
  margin-right: 0;
}

.input-form {
  /*max-width: 308px;*/
  margin-left: 15px;
}

.input-form-date {
  max-width: 131px;
  margin-left: 15px;
}

.label-search {
  font-size: 26px;
  margin-bottom: 0.5rem;
}

.playBtn{
  font-size: 50px;
   display: flex;
  font-weight: bold;
  text-align: center;
  justify-content: center;
  align-items: center;
  width: 120px;
  height: 120px;
  background-color: #cf3333;
  border-radius: 100%;
}

.playBox{
  display: flex;
  font-weight: bold;
  text-align: center;
  justify-content: center;
  align-items: center;
}

.button-box{
  padding: 15px;
}

.icon-search{
  color: #fbb540;
  text-shadow: 1px 1px 2px black;
}

.filter-box{
  max-width: 450px;
  margin-right: 30px;
}

/** Boutons recherche et refresh */
.button-custom{
  border: none;
  color: white;
  padding: 15px 32px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 18px;
  border-radius: 50px;
  box-shadow: rgba(75, 75, 75, 0.12) 0px 1px 3px, rgba(0, 0, 0, 0.24) 0px 1px 2px;
}

.button-custom:active{
  box-shadow: rgba(75, 75, 75, 0.12) 3px 3px 6px 0px inset, rgba(0, 0, 0, 0.24) -3px -3px 6px 1px inset;
}

#newsearch-button{
  background-color: #1f75d8;
}

#refresh-button{
  background-color: #f88436;
  margin-left: 10px;
}

.logo-button{
  font-size: 25px;
  border-right: 3px solid whitesmoke;
}

#refresh-button:hover .rotate-scale-up {
	-webkit-animation: rotate-scale-up 0.65s linear both;
	        animation: rotate-scale-up 0.65s linear both;
}

 @-webkit-keyframes rotate-scale-up {
  0% {
    -webkit-transform: scale(1) rotateZ(0);
            transform: scale(1) rotateZ(0);
  }
  50% {
    -webkit-transform: scale(1) rotateZ(180deg);
            transform: scale(1) rotateZ(180deg);
  }
  100% {
    -webkit-transform: scale(1) rotateZ(360deg);
            transform: scale(1) rotateZ(360deg);
  }
}
@keyframes rotate-scale-up {
  0% {
    -webkit-transform: scale(1) rotateZ(0);
            transform: scale(1) rotateZ(0);
  }
  50% {
    -webkit-transform: scale(1) rotateZ(180deg);
            transform: scale(1) rotateZ(180deg);
  }
  100% {
    -webkit-transform: scale(1) rotateZ(360deg);
            transform: scale(1) rotateZ(360deg);
  }
}

/* ANIMATED TITLE */
#newsearch-button:hover .wobble-hor-bottom {
	-webkit-animation: wobble-hor-bottom 0.8s both;
	        animation: wobble-hor-bottom 0.8s both;
}

 @-webkit-keyframes wobble-hor-bottom {
  0%,
  100% {
    -webkit-transform: translateX(0%);
            transform: translateX(0%);
    -webkit-transform-origin: 50% 50%;
            transform-origin: 50% 50%;
  }
  15% {
    -webkit-transform: translateX(-15px) rotate(-6deg);
            transform: translateX(-15px) rotate(-6deg);
  }
  30% {
    -webkit-transform: translateX(4px) rotate(6deg);
            transform: translateX(4px) rotate(6deg);
  }
  45% {
    -webkit-transform: translateX(-15px) rotate(-3.6deg);
            transform: translateX(-15px) rotate(-3.6deg);
  }
  60% {
    -webkit-transform: translateX(4px) rotate(2.4deg);
            transform: translateX(4px) rotate(2.4deg);
  }
  75% {
    -webkit-transform: translateX(-6px) rotate(-1.2deg);
            transform: translateX(-6px) rotate(-1.2deg);
  }
}
@keyframes wobble-hor-bottom {
  0%,
  100% {
    -webkit-transform: translateX(0%);
            transform: translateX(0%);
    -webkit-transform-origin: 50% 50%;
            transform-origin: 50% 50%;
  }
  15% {
    -webkit-transform: translateX(-15px) rotate(-6deg);
            transform: translateX(-15px) rotate(-6deg);
  }
  30% {
    -webkit-transform: translateX(4px) rotate(6deg);
            transform: translateX(4px) rotate(6deg);
  }
  45% {
    -webkit-transform: translateX(-15px) rotate(-3.6deg);
            transform: translateX(-15px) rotate(-3.6deg);
  }
  60% {
    -webkit-transform: translateX(4px) rotate(2.4deg);
            transform: translateX(4px) rotate(2.4deg);
  }
  75% {
    -webkit-transform: translateX(-6px) rotate(-1.2deg);
            transform: translateX(-6px) rotate(-1.2deg);
  }
}

/** MEDIA QUERIES */

@media (max-width: 1300px) {

}
</style>
