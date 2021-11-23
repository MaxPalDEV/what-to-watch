<template>
    <div class="d-flex justify-content-center">
      <div id="movie-box">
        <b-row>
          <b-col cols="3" class="text-center">
          <div v-if="poster">
            <img :src="poster" alt="" width="200">
          </div>
          <div v-if="!poster">
            <img src="../static/noposter.png" alt="" width="200">
          </div>
        </b-col>
        <b-col cols="8">
          <h2><i class="fas fa-film icon-search"></i> {{ movies.title }}</h2>
          <hr>
          <p>
            <span><i class="far fa-clock icon-search"></i> Durée : </span> {{movies.runtime}} min. |
            <span><i class="fas fa-globe-europe icon-search"></i> Origine : </span> <span v-if="movies.production_countries[0]">{{ movies.production_countries[0].iso_3166_1 }}</span> <span v-if="!movies.production_countries[0]">Non Communiqué</span>
          </p>
          <hr>
          <p>
            {{ movies.overview }}
          </p>
        </b-col>
        <b-col cols="1">
          <p id="voteAverage">
            {{ movies.vote_average }}
          </p>
        </b-col>
        </b-row>
      </div>
    </div>
</template>
<script>
export default {
  props: ['movies', 'poster'],
  transition: {

  }
}
</script>
<style scope>
  .page-enter-active,
.page-leave-active {
  transition: opacity 0.5s;
}
.page-enter,
.page-leave-active {
  opacity: 0;
}

#movie-box{
  margin-top: 2rem;
  background-color: #e07878;
  border-radius: 10px;
  padding: 20px;
  width: 1030px!important;
  /*box-shadow: rgba(0, 0, 0, 0.6) 0px 2px 4px 0px inset;*/
  box-shadow: rgba(0, 0, 0, 0.25) 0px 54px 55px, rgba(0, 0, 0, 0.12) 0px -12px 30px, rgba(0, 0, 0, 0.12) 0px 4px 6px, rgba(0, 0, 0, 0.17) 0px 12px 13px, rgba(0, 0, 0, 0.09) 0px -3px 5px;
  transition: max-width 0.5s;
}

#voteAverage{
  display: flex;
  font-weight: bold;
  text-align: center;
  justify-content: center;
  align-items: center;
  width: 50px;
  height: 50px;
  background-color: #cf3333;
  border-radius: 100%;
  box-shadow: rgba(60, 64, 67, 0.3) 0px 1px 2px 0px, rgba(60, 64, 67, 0.15) 0px 2px 6px 2px;
}
</style>
