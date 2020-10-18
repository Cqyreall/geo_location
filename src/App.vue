<template> 
  <div id="app">
    <link href="https://maxcdn.bootstrapcdn.com/font-awesome/4.7.0/css/font-awesome.min.css" rel="stylesheet"/>
    <h1 id="header">Travsort</h1>

    <label for="">Search For: </label>
    <select name="" id="" v-model="filter" v-on:change="filterSearch">
      <option value="" disabled>Select what you are looking for</option>
      <option value="restaurants">Restaurants</option>
      <option value="hotels">Hotels</option>
      <option value="parks">Parks</option>
      <option value="schools">Schools</option>
      <option value="hospitals">Hospitals</option>
      <option value="supermarkets">Super Markets</option>
      <option value="libraries">Libraries</option>
      <option value="airports">Airports</option>
      <option value="kfc">KFC</option>
      <option value="church">Churches</option>
      </select>

     <attraction-list :attractions="places" :longitude="longitude" :latitude="latitude"></attraction-list>
  <div class="maps" style="background-color: peach">
    <l-map
      v-if="showMap"
      :zoom="zoom"
      :center="center"
    >
      <l-tile-layer
        :url="url"
        :attribution="attribution"
      />
        <l-marker :lat-lng="latlng(latitude, longitude)">
        <l-tooltip :options="{ permanent: true, interactive: true }">
          <div>
            Your Current Location 
            <i class="fa fa-male" style="font-size:20px;color:green"></i>	
          </div>
        </l-tooltip>
      </l-marker>

      <div v-if="places.length > 0">
        <l-marker v-if="selectedAttraction !== choice" v-for="(choice, index) of places" :key="index" :lat-lng="latlng(choice.geometry.location.lat, choice.geometry.location.lng)" >
          <l-tooltip @mouseover.native="hover = true" @mouseleave.native="hover = false">
          <div v-if="hover">
            <img :src="choice.icon" alt="blank">
            {{choice.name}}
          <div>
            {{ choice.formatted_address}} 
          </div>   
          </div>
        </l-tooltip>

        </l-marker>
      </div>
      <div v-if="selectedAttraction">
         <l-marker :lat-lng="latlng(selectedAttraction.geometry.location.lat, selectedAttraction.geometry.location.lng)"  bindPopup=" hi">
          <l-icon  :iconUrl="selectedAttraction.icon" :iconSize="[40, 60]">
          </l-icon>
      </l-marker>
      </div>
    </l-map>

  </div>
  </div>
 
  
</template>

<script>
import { latLng } from "leaflet";;
import { LMap, LTileLayer, LMarker, LPopup, LTooltip, LIcon} from "vue2-leaflet";
import AttractionsList from './components/AttractionsList.vue';
import { eventBus } from './main.js';
import axios from "axios";
import school from './components/images/school.png'


export default {
  name: "app",
  components: {
    LMap,
    LTileLayer,
    LMarker,
    LPopup,
    LTooltip,
    LIcon,
    'attraction-list': AttractionsList,
    
  
  },
  data() {
    return {
      zoom: 6,
      center: latLng(55.3, -4),
      url: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
      attribution:
        '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors',
      marker: latLng("55", "-1.2"),
      withTooltip: latLng(47.41422, -1.250482),
      currentZoom: 11.5,
      currentCenter: latLng(55.3, -4),
      location: [],
      latitude: 55.8,
      longitude: -4.4,
      // latitude: 57.4778,
      // longitude: -4.2247,
      // latitude: 9.0765,
      // longitude: 7.3986,
      places: [],
      selectedAttraction: null,
      icon: school,
      hover: true,
      selectedAttraction_info: null,
      test: null,
      filter: "",
      // test: [],
      destination: "https://maps.gstatic.com/mapfiles/place_api/icons/v1/png_71/geocode-71.png",
      showMap: true,
      
    };
  },
  methods: {
    zoomUpdate(zoom) {
      this.currentZoom = zoom;
    },
    centerUpdate(center) {
      this.currentCenter = center;
    },
    latlng(lat, long){
      let position = latLng(lat, long)
      console.log(position)
      return position
    },

    filterSearch(){
      fetch(`https://cors-anywhere.herokuapp.com/https://maps.googleapis.com/maps/api/place/textsearch/json?key=key-s&query=${this.filter}-in-${this.location.city.toLowerCase()}`)
      .then(response => response.json())
      .then(data => this.places = data.results)
    },


    

  }, 

  computed: {
    longitudeLatitude(){
      this.latitude = this.location.latitude

      return this.latitude

    },
    long() {
      this.longitude = this.location.longitude
      return this.longitude
    },

    coordinates(){
        let coordinates = latLng(this.latitude, this.longitude)
        console.log(coordinates)
        return coordinates
    },

    

    

  },

  mounted(){
    fetch("https://freegeoip.app/json/")
    .then(response => response.json())
    .then(data => this.location = data)

    // axios.get()
    
    



    // fetch(`https://cors-anywhere.herokuapp.com/https://maps.googleapis.com/maps/api/place/nearbysearch/json?location=${
    //     this.latitude
    //   },${this.longitude}&types=parks&radius=${20 *
    //     1000}&key=key`)
    //   .then(response => response.json())
    //   .then(data => this.test = data.results)

    
    eventBus.$on('attraction-selected', (attraction) =>{
      this.selectedAttraction = attraction
    
    
    })

    eventBus.$on('attraction-info', (attraction_info) =>{
      this.selectedAttraction_info = attraction_info
    
    })

  }

};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  /* margin-top: 60px; */
  margin: 0%;
}

.maps {
  margin-left: 10%;
  width: 80%;
  margin-top: 7%;
  margin-bottom: 4%;
  height: 500px;

}

#header {
  margin: 0%;
  margin-top: 0%;
  background-color: blanchedalmond;
  padding: 10px;
}

</style>
