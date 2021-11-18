<template>
    <div class="d-flex justify-content-center">
      <b-col cols="6" id="search-box">
        <h1>{{ titre }}</h1>
      </b-col>
    </div>
</template>
<script>
export default {
  props: ['titre']
}
</script>
