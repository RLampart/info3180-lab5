<script setup>
import { ref, onMounted } from "vue";
let movies = ref([]);

function fetchImage(file){
   fetch ('/api/v1/posters/'+file)//.then((response) => response.data)
  .then((response) => {
      return response.url;
   })
};

function fetchMovies(){
    fetch('/api/v1/movies').then((response) => response.json())
  .then((data) => {
      console.log(data);
      for (let movie of data){
         fetch ('/api/v1/posters/'+movie.poster)//.then((response) => response.data)
  .then((response) => {
      let url =  response.url;
      movie.poster = url;
      console.log(movie.poster); 
      movies.value.push(movie);
   });   
      }  
   })
};
onMounted(() => {
 fetchMovies();
});

</script>

<template>
 <ul>
    <li v-for="movie in movies" :key="movie.id" class=" col-md-10">
      <div class="card">
        <div class="card-img-top">
            <img :src=movie.poster alt="MoviePoster"/>   
        </div>
        <div class="card-body">
            <h5 class="card-title">{{ movie.title }}</h5>
            <p class="card-text">
                {{ movie.description }}
            </p>
        </div>
    </div>
    </li>
 </ul>
</template>

<style>
ul{
    display: grid;
    grid-template-columns: auto auto;
    row-gap: 10px;
    column-gap: 10px;
}
.card {
  flex-direction: row;
}
.card-img-top{
  width: 60%;
}
.card-img-top img {
  height: 250px;
}
.card-body {
  width: 100%;
}
.card-text{
  text-wrap:pretty;
}
li{
    list-style: none;
}
</style>