<template>
  <div v-if="locations.length > 0">
   <div ref="placeCard" class="pac-card" id="pac-card">
    <div id="pac-container">
     <input type="text" id="gmapInput" ref="gmapInput" label="" autocomplete="off"></input>
    </div>
    </div>
     <div id="infowindow-content">
      <img src="" width="16" height="16" id="place-icon">
      <span id="place-name"  class="title"></span><br>
      <span id="place-address"></span>
    </div>
    <GMap
      ref="gMap"
      :cluster="{ options: { styles: clusterStyle } }"
      :center="{ lat: locations[0].lat, lng: locations[0].lng }"
      :options="{ fullscreenControl: false, styles: mapStyle }"
      :zoom="9"
      @click="mapClick"
      @init="initMap"
      @map="mapLoaded"
    >
      <GMapMarker
        v-for="(location, index) in locationsList"
        :key="location.id"
        :position="{ lat: location.lat, lng: location.lng }"
        @click="currentLocation = location"
      >
     
      </GMapMarker>
    </GMap>
   {{currentLocation}} currentLocation
    
  </div>
</template>

<script>
export default {
  components: {
 
  },
  methods: {
    mapClick(location) {
      console.log("lll :", location.event.latLng.lat(), location.event.latLng.lng());
     
    },
    initMap(google){
      this.google = google
      console.log("map initlaized");
       this.addAutoComplete()
    },
    mapLoaded(map){
      console.log('loaded :', map);
      this.map = map
     
    },
    addAutoComplete(){
      console.log('addAutoComplete :', this.map);
      console.log('this.google :', this.google);
       var card = this.$refs.placeCard
      
      this.autocomplete = new this.google.maps.places.Autocomplete(this.$refs.gmapInput);
      this.autocomplete.bindTo('bounds', this.map);
      this.autocomplete.setFields(
            ['address_components', 'geometry', 'icon', 'name']);
            console.log('bound :');
      console.log('autocomplete :',this.autocomplete);
     var marker = new google.maps.Marker({
          map: this.map,
          anchorPoint: new google.maps.Point(0, -29)
        });
     this.autocomplete.addListener('place_changed', ()=> {
         
       
          var place = this.autocomplete.getPlace();
          console.log('place :', place);
          if (!place.geometry) {
            // User entered the name of a Place that was not suggested and
            // pressed the Enter key, or the Place Details request failed.
            window.alert("No details available for input: '" + place.name + "'");
            return;
          }
          console.log('place.geometry.location :', place.geometry.location);
          // If the place has a geometry, then present it on a map.
          if (place.geometry.viewport) {
            this.map.fitBounds(place.geometry.viewport);
          } else {
            this.map.setCenter(place.geometry.location);
            this.map.setZoom(17);  // Why 17? Because it looks good.
            console.log('place.geometry.location.lat() :', );
            
            console.log('place.geometry.location :', {lat: place.geometry.location.lat(), lng: place.geometry.location.lng()});
            
            // this.map['markers'].push(place.geometry.location)
            
            
          }
          

          var address = '';
          if (place.address_components) {
            address = [
              (place.address_components[0] && place.address_components[0].short_name || ''),
              (place.address_components[1] && place.address_components[1].short_name || ''),
              (place.address_components[2] && place.address_components[2].short_name || '')
            ].join(' ');
          }
         
          marker.setPosition(place.geometry.location);
          this.currentLocation ={lat: place.geometry.location.lat(), lng: place.geometry.location.lng(), address}
          this.map.markers = [].push(marker)
          console.log('this.locations :', this.map.markers.push(marker));
        });
    }
  },
 
  mounted() {
    if (process.client) {
      //  console.log('this :', window.google.maps);
   
    }
    console.log(" try to get location:");
   
  
      this.$geolocation.getCurrentPosition().then(object => {
        console.log("object :", object.coords);
        this.locations.push({
          lat: object.coords.latitude,
          lng: object.coords.longitude
        });
        
   
      });


      
    
  },
  computed: {
    locationsList() {
      return this.locations 
    }
  },
  data() {
    return {
      google: null,
      map: null,
      inRange: false,
      currentLocation: {},
      locations: [],
      place: "",
      autocomplete:null,
      mapStyle: [],
      clusterStyle: [
        {
          url:
            "https://developers.google.com/maps/documentation/javascript/examples/markerclusterer/m1.png",
          width: 56,
          height: 56,
          textColor: "#fff"
        }
      ]
    };
  }
};
</script>


