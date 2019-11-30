<template>
  <div  v-if="locations.length >0">
   
    <GMap
      ref="gMap"
      
      :cluster="{options: {styles: clusterStyle}}"
      :center="{lat: locations[0].lat, lng: locations[0].lng}"
      :options="{fullscreenControl: false, styles: mapStyle}"
      :zoom="9"
    >
      <GMapMarker
       
        v-for="location in locations"
        :key="location.id"
        :position="{lat: location.lat, lng: location.lng}"
       
        @click="currentLocation = location"
      >
        <GMapInfoWindow>
          <code>
            שם: 
            ארוע:
            lat: {{ location.lat }},
            lng: {{ location.lng }}
          </code>
        </GMapInfoWindow>
      </GMapMarker>
    </GMap>
    
   
  </div>
</template>

<script>
import Logo from "~/components/Logo.vue";
import VuetifyLogo from "~/components/VuetifyLogo.vue";
import { GMap, GMapMarker, GMapInfoWindow } from "nuxt-gmaps";
export default {
  components: {
    Logo,
    VuetifyLogo
  },
 watch: {
    inRange(reached) {
      if (reached !== true) return
      console.log('Congratulations, you arrived')
      this.$geolocation.watch = false // Stop watching location
    }
  },
  mounted () {
    console.log(' try to get location:' );
    this.$geolocation.getCurrentPosition().then(object=>{
      console.log('object :', object.coords);
       this.locations.push({lat: object.coords.latitude, lng: object.coords.longitude})
    }) 
},
  data() {
    return {
      inRange: false,
      currentLocation: {},
      locations: [
        {
          lat: 32.1518363,
          lng: 34.8168214
        },{
          lat: 32.218021, 
          lng:34.844743
        }
      
      ],

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
