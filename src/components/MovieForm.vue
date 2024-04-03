<template>
      <div v-if = "feedback" :class="category">
      <ol>
        <li v-for="message in messages">{{ message }}</li>
      </ol>
       
    </div>

    <div>
        <form id='movieForm' @submit.prevent="saveMovie" method="post" enctype="multipart/form-data">
        <div class="form-group col-md-3">
        <label for="title" class="form-label">Movie Title</label>
        <input type="text" name="title" class="form-control" />
        </div>
        <div class="form-group col-md-6">
        <label for="description" class="form-label">Movie Description</label>
        <textarea name="description" class="form-control"></textarea>
        </div>  
       <div class="form-group col-md-3">
        <label for="poster" class="form-label">Movie Poster</label>
        <input type="file" name="poster" class="form-control" />
       </div>
      <button type="submit" name="submit" class="btn btn-primary">Upload Movie</button>
      </form>
    </div>
</template>

<script setup>
import { ref, onMounted  } from "vue";
let csrf_token = ref("");
var feedback = ref(false);
let messages = "";
let category = ref("alert alert-danger");
function getCsrfToken() {
  fetch('/api/v1/csrf-token').then((response) => response.json())
  .then((data) => {
      console.log(data);
      csrf_token.value = data.csrf_token;
   })
   } ;
onMounted(() => {
 getCsrfToken();
}); 
function saveMovie(){
    feedback.value = false;
    let movieForm = document.getElementById('movieForm');
    let form_data = new FormData(movieForm);
    fetch("/api/v1/movies", {
      method: 'POST',
      body: form_data,
      headers: {'X-CSRFToken': csrf_token.value }
    }).then(function (response) {
       return response.json();
     }).then(function (data) {
 // display a success message
      console.log(data);
      movieForm.reset();
      if (data['message'] != undefined){
         category = "alert alert-success";
         messages = [data["message"]];
      }else{
        category = "alert alert-danger";
         messages = data;
      }feedback.value = true; 

     }).catch(function (error) {
       console.log(error);
       movieForm.reset();
       category = "alert alert-danger";
       messages = error;
       feedback.value = true;  
     });
     }
    
</script>

<style>
/* Add any component specific styles here */
.form-group{
    padding-bottom: 10px;
}
</style>