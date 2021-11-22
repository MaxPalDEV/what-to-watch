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
          <button id="newsearch-button" class="button-custom d-flex" @click="newSearch"><span class="logo-button"><i class="fas fa-search"></i>&nbsp;</span><span class="align-self-center ml-2"> Nouvelle Recherche</span></button>  <button id="refresh-button" class="button-custom d-flex" @click="searchMovie"><span class="logo-button"><i class="fas fa-sync-alt"></i>&nbsp;</span><span class="align-self-center ml-2">Autre Proposition</span></button>
        </div>
      <div id="search-box" v-if="!show">
        <b-row>
<b-col cols="8">
           <b-col cols="10">
          <label class="label-search" for="genre-select"><i class="fas fa-arrow-circle-right"></i> Quel genre de film ?</label>
        </b-col>
        <b-col cols="10" class="ml-4">
          <b-form-select v-model="genre_selected" name="genre-select" class="mb-3 input-form">
            <template #first>
              <b-form-select-option :value="null" disabled>-- Sélectionner un genre --</b-form-select-option>
            </template>
            <b-form-select-option :value="genre.id" v-for="genre in genres.genres" v-bind:key="genre.id">{{ genre.name }}</b-form-select-option>
          </b-form-select>
        </b-col>
        <b-col cols="10" class="mt-1">
          <p class="label-search"><i class="fas fa-arrow-circle-right"></i> Période de sortie : </p>
        </b-col>
        <b-col cols="10">
          <b-row class="ml-4">
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
        <b-col cols="10" class="mt-1">
          <p class="label-search"><i class="fas fa-arrow-circle-right"></i> Quelle plateforme ? </p>
        </b-col>
        <b-col cols="10" class="ml-4">
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
    },
    newSearch(){
      this.show = false;
    }
  },
  computed : {
    years () {
      const year = new Date().getFullYear()
      return Array.from({length: year - 1969}, (value, index) => 1970 + index)
    }
  }
};
</script>

<style>
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
  max-width: auto;
  margin-left: 15px;
}

.input-form-date {
  max-width: 125px;
  margin-left: 15px;
}

.label-search {
  font-size: 26px;
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

/** BTN PLAY */

.clapboard{
  background-image: url(../static/clapperboard.png);
  background-position: center;
  background-repeat: no-repeat;
  background-size: 50%;
}

.clapboard:hover{
  background-image: url(../static/clapperboard_close.png);
  cursor: pointer;
}

#wrapper{
  display:flex;
  align-items:center;
  justify-content:center;
}
.my-super-cool-btn{
  position:relative;
  text-decoration:none;
  color:#fff;
  letter-spacing:1px;
  font-size:2rem;
  box-sizing:border-box;
}
.my-super-cool-btn span{
  position:relative;
  box-sizing:border-box;
  display:flex;
  align-items:center;
  justify-content:center;
  width:200px;
  height:200px;
}
.my-super-cool-btn span:before{
  content:'';
  width:100%;
  height:100%;
  display:block;
  position:absolute;
  border-radius:100%;
  border:7px solid #F3CF14;
  box-sizing:border-box;
  transition: all .85s cubic-bezier(0.25, 1, 0.33, 1);
  box-shadow: 0 30px 85px rgba(0,0,0,0.14), 0 15px 35px rgba(0,0,0,0.14);
}
.my-super-cool-btn:hover span:before{
  transform:scale(0.8);
  box-shadow: 0 20px 55px rgba(0,0,0,0.14), 0 15px 35px rgba(0,0,0,0.14);
}
.my-super-cool-btn .dots-container{
  opacity:0;
  animation: intro 1.6s;
  animation-fill-mode: forwards;
}
.my-super-cool-btn .dot{
  width:8px;
  height:8px;
  display:block;
  background-color:#F3CF14;
  border-radius:100%;
  position:absolute;
  transition: all .85s cubic-bezier(0.25, 1, 0.33, 1);
}
.my-super-cool-btn .dot:nth-child(1){
  top:50px;
  left:50px;
  transform:rotate(-140deg);
  animation: swag1-out 0.3s;
  animation-fill-mode: forwards;
  opacity:0;
}
.my-super-cool-btn .dot:nth-child(2){
  top:50px;
  right:50px;
  transform:rotate(140deg);
  animation: swag2-out 0.3s;
  animation-fill-mode: forwards;
  opacity:0;
}
.my-super-cool-btn .dot:nth-child(3){
  bottom:50px;
  left:50px;
  transform:rotate(140deg);
  animation: swag3-out 0.3s;
  animation-fill-mode: forwards;
  opacity:0;
}
.my-super-cool-btn .dot:nth-child(4){
  bottom:50px;
  right:50px;
  transform:rotate(-140deg);
  animation: swag4-out 0.3s;
  animation-fill-mode: forwards;
  opacity:0;
}
.my-super-cool-btn:hover .dot:nth-child(1){
  animation: swag1 0.3s;
  animation-fill-mode: forwards;
}
.my-super-cool-btn:hover .dot:nth-child(2){
  animation: swag2 0.3s;
  animation-fill-mode: forwards;
}
.my-super-cool-btn:hover .dot:nth-child(3){
  animation: swag3 0.3s;
  animation-fill-mode: forwards;
}
.my-super-cool-btn:hover .dot:nth-child(4){
  animation: swag4 0.3s;
  animation-fill-mode: forwards;
}
@keyframes intro {
   0% {
     opacity:0;
  }
  100% {
     opacity:1;
  }
}
@keyframes swag1 {
   0% {
     top:50px;
     left:50px;
     width:8px;
  }
  50% {
    width:30px;
    opacity:1;
  }
  100% {
     top:20px;
     left:20px;
     width:8px;
     opacity:1;
  }
}
@keyframes swag1-out {
   0% {
     top:20px;
     left:20px;
     width:8px;
  }
  50% {
     width:30px;
    opacity:1;
  }
  100% {
     top:50px;
     left:50px;
     width:8px;
    opacity:0;
  }
}
@keyframes swag2 {
   0% {
     top:50px;
     right:50px;
     width:8px;
  }
  50% {
    width:30px;
    opacity:1;
  }
  100% {
     top:20px;
     right:20px;
     width:8px;
     opacity:1;
  }
}
@keyframes swag2-out {
   0% {
     top:20px;
     right:20px;
     width:8px;
  }
  50% {
     width:30px;
    opacity:1;
  }
  100% {
     top:50px;
     right:50px;
     width:8px;
    opacity:0;
  }
}
@keyframes swag3 {
   0% {
     bottom:50px;
     left:50px;
     width:8px;
  }
  50% {
    width:30px;
    opacity:1;
  }
  100% {
     bottom:20px;
     left:20px;
     width:8px;
     opacity:1;
  }
}
@keyframes swag3-out {
   0% {
     bottom:20px;
     left:20px;
     width:8px;
  }
  50% {
     width:30px;
    opacity:1;
  }
  100% {
     bottom:50px;
     left:50px;
     width:8px;
    opacity:0;
  }
}
@keyframes swag4 {
   0% {
     bottom:50px;
     right:50px;
     width:8px;
  }
  50% {
    width:30px;
    opacity:1;
  }
  100% {
     bottom:20px;
     right:20px;
     width:8px;
     opacity:1;
  }
}
@keyframes swag4-out {
   0% {
     bottom:20px;
     right:20px;
     width:8px;
  }
  50% {
     width:30px;
    opacity:1;
  }
  100% {
     bottom:50px;
     right:50px;
     width:8px;
    opacity:0;
  }
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
  box-shadow: rgba(0, 0, 0, 0.12) 0px 1px 3px, rgba(0, 0, 0, 0.24) 0px 1px 2px;
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

/** MEDIA QUERIES */

@media (max-width: 1300px) {

}
</style>
