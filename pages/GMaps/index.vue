<template>
<div style="height:100%" >
 <v-card
    
    class="mx-auto"
  >
    <v-list-item>
       
         <v-avatar>
      <img
        src="~/assets/images/Gamie.png"
        alt="Gamie"
      >
    </v-avatar>
      <v-list-item-content>
        <v-list-item-title class="headline">{{HeadText}}</v-list-item-title>
        <v-list-item-subtitle>{{DetailText}}</v-list-item-subtitle>
      </v-list-item-content>
    </v-list-item>


  </v-card>
<v-spacer></v-spacer>

    <GmapMap ref="mapRef" 
  :center="{lat:currentLocation.lat, lng:currentLocation.lng}"
  :zoom="15"
  map-type-id="terrain"
  style="width: 100%; height: 100%"
   :options="{
   zoomControl: true,
   mapTypeControl: false,
   scaleControl: false,
   streetViewControl: false,
   rotateControl: false,
   fullscreenControl: false,
   disableDefaultUi: false
 }"
>
  <GmapMarker
    :key="1"
    :position="currentLocation"
    :clickable="true"
    :draggable="false"
    :icon="{ url: require('~/assets/images/kidsup.png')}" />

  />
    <GmapMarker
    :key="2"
    :position="StFrancis"
    :clickable="true"
    :draggable="false"
    :icon="{ url: require('~/assets/images/Gamie.png')}" />
  />

    </GmapMap>

</div>    
</template>
<script>
import {gmapApi} from 'vue2-google-maps'
 


export default {
    
   //components: {DirectionsRenderer},   
  computed: {
    google: gmapApi
  },

    layout: 'none',
    data(){
    return{
    HeadText: "",
    DetailText: "",
    StFrancis: {lat: 13.913, lng: 100.556},
    locations: [
      {lat: 13.760, lng: 100.53},
      {lat: 45.8150, lng: 15.9819},
      {lat: 45.12, lng: 16.21}
    ],
 
    pins: {
      car: 'https://static.wixstatic.com/media/cb1e74_759db232728140389ecfb592e79d3e10~mv2.png/v1/fill/w_51,h_44,al_c,q_80,usm_0.66_1.00_0.01/cb1e74_759db232728140389ecfb592e79d3e10~mv2.webp',
      selected: 'data:image/png;base64,iVBOAAANSUhEUgAAAB...',
      notSelected: 'data:image/png;base64,iVBORw0CAM...'
    },
    test: 1,
    // mapStyle: [
    //   ...
    // ],
 
    currentLocation: {lat: Number(this.$route.query.lat), lng:  Number(this.$route.query.lng)} ,// {lat: 13.760, lng: 100.53},
   

  }},

 // asyncData({isDev, route, store, env, params, query, req, res, redirect, error}) {
     // console.log(query)
     // console.log(params)
    //   if (params) {
    //       this.data.currentLocation.lat = parseFloat(params.lat)
    //       this.data.currentLocation.lng = parseFloat(params.lng)
    //   }
  //},
  asyncData({isDev, route, store, env, params, query, req, res, redirect, error}) {
       
  }, 
  mounted () {
     
          this.$refs.mapRef.$mapPromise.then((map) => {
        //    console.log(map)
        //    console.log(this.$data.query)
        //    console.log(this.$data.currentLocation)
            let directionsService = new this.google.maps.DirectionsService();
            let directionsDisplay = new google.maps.DirectionsRenderer({
      suppressMarkers: true
    });
            
             directionsDisplay.setMap(map);
            var request = {
                    origin: this.$data.currentLocation,
                    destination: this.$data.StFrancis,
                    travelMode: google.maps.TravelMode.DRIVING
                };
            let data = this.$data
            directionsService.route(request, function (response, status) {
                    if (status == google.maps.DirectionsStatus.OK) {
                        directionsDisplay.setDirections(response);
                        console.log(response)
                        console.log(data)
                        data.HeadText = "Gamie (Queue: " + Math.floor((Math.random() * 100) + 1).toString() + ")"
                        if (response.routes){
                            let route = response.routes[0]
                            if (route){
                                let leg = route.legs
                                if (leg) {
                                     data.DetailText =  "Distance : " + leg[0].distance.text + " / Duration : " + leg[0].duration.text

                                }
                            }
                            
                        }
                        console.log( data)
                        
                    }
                });
    })
  }
 
}
</script>