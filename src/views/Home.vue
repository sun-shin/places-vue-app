<template>
  <div class="home">
    <h1>{{ message }}</h1>

     <ul>
      <li v-for="error in errors">{{ error }}</li>
    </ul>

    <div>
      Name:<input type="text" v-model="newPlacename"  ><br>
      Address: <input type="text" v-model="newPlaceaddress"> <br>
      <button v-on:click="createPlace()">Add Place</button>
    </div>

    <div v-for="place in places">
      <h2>{{ place.name }}</h2><br>
      <h2>{{ place.address }}</h2><br>
      <button v-on:click="showPlace(place)">More Info</button>
    </div>

    <dialog id="place-details">
      <form action="dialog">
        <h2>Place Info</h2>
        <p>Name: <input type="text" v-model="currentPlace.name"></p>
        <p>Address: <input type="text" v-model="currentPlace.address"></p>
        <button v-on:click="updatePlace(currentPlace)">Update Place</button>
        <button v-on:click="destroyPlace(currentPlace)">Delete Place</button>
        <button>Close</button>
      </form>
    </dialog>

   
  </div>
</template>

<style>
</style>

<script>
import axios from "axios";
export default {
  data: function() {
    return {
      message: "Check out our places!",
      places: [],
      newPlacename: "",
      newPlaceaddress: "",
      currentPlace: {},
      errors: [],
    };
  },
  created: function() {
    this.indexPlaces();
  },
  methods: {
    indexPlaces: function() {
      axios.get("/api/places").then(response => {
        console.log(response.data);
        this.places = response.data;
      });
    },
    createPlace: function() {
      var params = {
        name: this.newPlacename,
        address: this.newPlaceaddress
      };
      axios.post("/api/places", params).then(response => {
        console.log("Success", response.data);
        this.places.push(response.data);
      })
        .catch(error => {
          this.errors = error.response.data.errors;
        });
    },
    showPlace: function (place) {
      console.log(place.name);
      this.currentPlace = place;
      document.querySelector("#place-details").showModal();
    },
    updatePlace: function (place) {
      var params = {
        name: place.name,
        address: place.address,
      };
      axios.patch(`/api/places/${place.id}`, params).then(response => {
        console.log("Success", response.data);
      })
        .catch(error => {
          this.errors = error.response.data.errors;
        });
    },
    destroyPlace: function (place) {
      axios.delete(`/api/places/${place.id}`).then(response => { 
        console.log("Success", response.data);
        var index = this.places.indexOf(place);
        this.places.splice(index, 1);
      })
        .catch(error => {
          this.errors = error.response.data.errors;
        });
    },
  }
};
</script>