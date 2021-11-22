<template>
<div>
    <div class="d-flex justify-content-center">
      <b-col cols="6" id="search-box">
        <b-row>
          <b-col cols="10">
        <b-row class="row-style">
          <b-col cols="5">
            <label class="label-search" for="genre-select"><i class="fas fa-arrow-circle-right"></i> Quel genre de film ?</label>
          </b-col>
          <b-col cols="7">
            <b-form-select v-model="genre_selected" name="genre-select" class="mb-3">
            <template #first>
              <b-form-select-option :value="null" disabled>-- Sélectionner un genre --</b-form-select-option>
            </template>
            <b-form-select-option :value="genre.id" v-for="genre in genres.genres" v-bind:key="genre.id">{{ genre.name }}</b-form-select-option>
          </b-form-select>
          </b-col>
        </b-row>

        <b-row class="row-style">
          <b-col cols="5">
            <label class="label-search" for="date-start-select"><i class="fas fa-arrow-circle-right"></i> Film sorti entre </label>
          </b-col>
          <b-col cols="7">
            <b-row class="row-style">
              <b-col cols="auto">
              <b-form-datepicker id="date-start-select" v-model="value_date_start" class="mb-2 input-form-date" :date-format-options="{ year: 'numeric', month: 'numeric', day: 'numeric' }" placeholder="-- Date --"></b-form-datepicker>
              </b-col>
              <b-col cols="1">
              <label class="label-search" for="date-end-select">et</label>
              </b-col>
              <b-col cols="auto">
              <b-form-datepicker id="date-end-select" v-model="value_date_end" class="mb-2 input-form-date" :date-format-options="{ year: 'numeric', month: 'numeric', day: 'numeric' }" placeholder="-- Date --"></b-form-datepicker>
              </b-col>
            </b-row>
          </b-col>
        </b-row>

        <b-row class="row-style">
          <b-col cols="5">
          <label class="label-search" for="platform-select"><i class="fas fa-arrow-circle-right"></i> Quelle plateforme ?</label>
          </b-col>
          <b-col cols="7">
          <b-form-select v-model="platform_selected" name="platform-select" class="mb-3">
            <template #first>
              <b-form-select-option :value="null" disabled>-- Sélectionner une plateforme --</b-form-select-option>
            </template>
            <b-form-select-option :value="platform.provider_id" v-for="platform in platforms.results" v-bind:key="platform.provider_id">{{ platform.provider_name }}</b-form-select-option>
          </b-form-select>
          </b-col>
        </b-row>
          </b-col>
          <b-col cols="2">
            <button @click="searchMovie">test</button>
          </b-col>

        </b-row>

      </b-col>
    </div>
</div>
</template>

<script>

export default {

}
</script>

<style>

</style>

