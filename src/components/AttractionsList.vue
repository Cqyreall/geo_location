<template>
  <div class="list">
      <link href="https://fonts.googleapis.com/css2?family=Baloo+2&family=Baloo+Tammudu+2:wght@500&display=swap" rel="stylesheet">
      <label for="">Select Destination: </label>
      <select name="" id="" v-model="selectedAttraction" v-on:change="handler">
          <option value="" disabled>Select attraction</option>
          <option  v-for="(attraction, index) in attractions " :value="attraction" :key="index"> {{ attraction.name }}</option>
      </select>
      <div class="details">
          <attraction-detail :attraction="selectedAttraction" :latitude="latitude" :longitude="longitude"></attraction-detail>
      </div>
      
      

  </div>
</template>

<script>

import AttractionDetail from './AttractionsDetail'
import { eventBus } from '../main.js';
export default {
    name: 'attraction-list',
    props: ['attractions', 'latitude', 'longitude'],

  
    data(){
        return {
            selectedAttraction: null,
            result: false

        }
    },

    components: {
        'attraction-detail': AttractionDetail
    },

    methods: {
        handler(){

           eventBus.$emit('attraction-selected', this.selectedAttraction)
        }
    }




   
}
</script>

<style>

.list{
    margin-top: 1%;
    font-family: 'Baloo Tammudu 2', cursive;
    font-size: 22px;
    font-weight: bold;
    background-color: lightblue;
    padding: 12px;
    border-radius: 10px;
    
}


</style>