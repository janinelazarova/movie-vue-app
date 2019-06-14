<template>
  <div class="display">
    <h1>New Movies</h1>
    <div>
      Title: <input type="text" v-model="newMovieTitle"><br>
      Year: <input type="text" v-model="newMovieYear"><br>
      Plot: <input type="text" v-model="newMoviePlot"><br>
      Director: <input type="text" v-model="newMovieDirector"><br>
      English (yes): <input type="radio" value="true" v-model="newMovieEnglish">
      English (no): <input type="radio" value="false" v-model="newMovieEnglish"><br>
      <button v-on:click="createMovie()">Create Movie</button>
    </div>
    <h1>All Movies</h1>
    <div v-for="movie in movies">
      <h2>{{ movie.title }}</h2>
      <p>{{ movie.year }}</p>
      <button v-on:click="showMovie(movie)">Show More></button>
      <div v-if="currentMovie === movie">
      <p>{{ movie.plot }}</p>
      <p>{{ movie.director }}</p>
      <p>{{ movie.english }}</p>
      <div>
        Title: <input type="text" v-model="movie.title">
        Year: <input type="text" v-model="movie.year">
        Plot: <input type="text" v-model="movie.plot">
        Director: <input type="text" v-model="movie.director">
        <button v-on:click="updateMovie">Update Movie</button>
        <button v-on:click="destroyMovie">Delete Movie</button>
      </div>
    </div>
  </div>
</div>
</template>

<script>
import axios from "axios";


export default {
  data: function() {
    return {
      movies: [],
      currentMovie: {},
      newMovieTitle: " ",
      newMovieYear: " ",
      newMoviePlot: " ",
      newMovieDirector: " ",
      newMovieEnglish: " ",
    };
  },
  created: function() {
    axios.get("api/movies").then(response => {
      this.movies = response.data;
    });
  },
  methods: {
    createMovie: function() {
      var params = {
        title: this.newMovieTitle,
        year: this.newMovieYear,
        plot: this.newMoviePlot,
        director: this.newMovieDirector,
        english: this.newMovieEnglish
      };
      axios.post("api/movies", params).then(response => {
        this.movies.push(response.data);
      });
    },
    showMovie: function(movie) {
      if (this.currentMovie === movie) {
        this.currentMovie = {};
      } else {
        this.currentMovie = movie;
      }  
    },
    updateMovie: function(movie) {
      var params = {
        title: movie.title,
        year: movie.year,
        plot: movie.plot,
        director: movie.director
      };
      axios
        .patch("api/movies/" + movie.id, params)
        .then(response => {
          this.currentMovie = {};
        });
    },
    destroyMovie: function(movie) {
      axios
        .delete("api/movies/" + movie.id)
        .then(response => {
          var index = this.movies.indexOf(movie);
          this.movies.splice(index, 1);
        });
    }
  }  
};
</script>
