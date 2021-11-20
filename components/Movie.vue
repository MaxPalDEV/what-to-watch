<template>
    <div class="d-flex justify-content-center">
      <b-col cols="6" id="search-box" v-for="movie in movies" v-bind:key="movie.id">
        <b-row>
          <b-col cols="3" class="text-center">
          <img :src="poster" alt="" width="200">
        </b-col>
        <b-col cols="8">
          <h2>{{ movie.title }}</h2>
          <p>
            <span>Durée : </span> {{movie.runtime}} min. |
            <span>Origine : </span> <span v-if="movie.production_countries != null">{{ movie.production_countries[0].iso_3166_1 }}</span>
          </p>
          <p>
            {{ movie.overview }}
          </p>
        </b-col>
        <b-col cols="1">
          <p id="voteAverage">
            {{ movie.vote_average }}
          </p>
        </b-col>
        </b-row>
      </b-col>
    </div>
</template>
<script>
export default {
  props: ['movies', 'poster']
}
</script>
