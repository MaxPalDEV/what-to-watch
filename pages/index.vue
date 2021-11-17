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
          <label for="">Quel genre de film ?</label>
          <b-form-select v-model="genre_selected" class="mb-3" id="input-form">
            <template #first>
        <b-form-select-option :value="null" disabled>-- Sélectionner un genre --</b-form-select-option>
      </template>
            <b-form-select-option :value="genre.id" v-for="genre in genres.genres" v-bind:key="genre.id">{{genre.name}}</b-form-select-option>
          </b-form-select>
        </b-row>
      </b-col>
    </div>
  </div>
</template>

<script>
export default {
  async fetch(){
    this.genres = await fetch('https://api.themoviedb.org/3/genre/movie/list?api_key=d26ad6e614a59a93420fc409d7922d8d&language=fr-FR').then((res) => {
      if (res.ok) {
        return res.json()
      }
      throw new Error(res.status) // Traitement des erreurs

  }
    )},
  data() {
      return {
        genre_selected: null,
        genres: []
      }
    }
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

#search-box .row-style{
  margin-left: 0!important;
  margin-right: 0;
}

#input-form{
  width: 400px;
  margin-left: 15px;
}
</style>
