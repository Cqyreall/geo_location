<template>
  <div v-if="attraction">
    <link href="https://maxcdn.bootstrapcdn.com/font-awesome/4.7.0/css/font-awesome.min.css" rel="stylesheet"/>

      <h2 v-on:click="handleClick">Name: {{ attraction.name }}</h2>

      <h3> <i class="fa fa-map-marker" style="font-size:24px;color:green"></i> Address: {{ attraction.formatted_address }}</h3>
      <h2 v-if="Math.round(attraction.rating) === 5">Ratings: <span>&#11088; 	&#11088; 	&#11088; 	&#11088; &#11088;</span></h2>
      <h2 v-if="Math.round(attraction.rating) >= 4 && Math.round(attraction.rating) < 5">Ratings: <span>&#11088; 	&#11088; 	&#11088; 	&#11088;</span></h2>
      <h2 v-if="Math.round(attraction.rating) >=3 && Math.round(attraction.rating) < 4">Ratings: <span>&#11088; 	&#11088; 	&#11088; </span></h2>
      <div v-if="attraction.business_status">
          <h3 v-if="attraction.business_status !== this.open" >Temporarily Closed &#10060; </h3>
      </div>
      <div v-if="attraction.opening_hours">
          <h3 v-if="attraction.opening_hours.open_now" > Currently Open &#9989;</h3>
          <h3 v-if="!attraction.opening_hours.open_now"> Currently Closed 	&#10060; </h3>
      </div>
      <h2 >Straight Line Distance: {{ distanceFromPoint()  }} Kilometres</h2> 
        <h3 v-if="attraction.photos"> More Information at:<a :href="convtoSting(attraction.photos[0].html_attributions[0])">{{ attraction.name }}</a></h3>
        <h3 v-if="attraction_info">	&#128222; {{ attraction_info.international_phone_number }} </h3>
      <button v-on:click="handleClick">View Phone Number</button>

      
  </div>
</template>

<script >
import { eventBus } from '../main.js';

export default {
    name: 'attraction-detail',
    props: ['attraction', 'latitude', 'longitude'],
    data(){
        return{
            open: "OPERATIONAL",
            attraction_info: null
        }
        
    },
    methods:{
        handleClick(){

             fetch(`https://cors-anywhere.herokuapp.com/https://maps.googleapis.com/maps/api/place/details/json?place_id=${this.attraction.place_id}&key=AIzaSyBeXDkg2kK4Ubtz0v0B8Nx2lQHZvcKYs-s`)
            .then(response => response.json())
            .then(data => this.attraction_info = data.result)
            .then(data => eventBus.$emit('attraction-info', data))
            // eventBus.$emit('attraction-info', this.attraction_info)
           
        },

        distanceFromPoint(){
            let radius = 6371
            let degreeLatitude = this.degreeToRad(this.latitude - this.attraction.geometry.location.lat)
            let degreeLongitude = this.degreeToRad(this.longitude - this.attraction.geometry.location.lng)
            let firstDistance = Math.sin(degreeLatitude/2) * Math.sin   (degreeLatitude/2) +
              Math.cos(this.degreeToRad(this.attraction.geometry.location.lat)) * Math.cos(this.degreeToRad(this.latitude)) * 
          Math.sin(degreeLongitude/2) * Math.sin(degreeLongitude/2);
      
            let variable = 2 * Math.atan2(Math.sqrt(firstDistance), Math.sqrt(1-firstDistance));
            let distance = (radius * variable).toFixed(2)
            console.log(distance)
            return distance
      
        },

        

        degreeToRad(deg){
            let rad = deg * (Math.PI/180)
            return rad

    },

     convtoSting(str){
            let frame = document.createElement('iframe');
            frame.style.display = 'none';
            document.body.appendChild(frame);             
            frame.contentDocument.open();
            frame.contentDocument.write(str);
            frame.contentDocument.close();
            let el = frame.contentDocument.body.firstChild;
            document.body.removeChild(frame);
            return el;
        },
         
        
    },


    
}
</script>

<style>



</style>
