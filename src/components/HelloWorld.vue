<template>
  <div>

    <div id='map' style='width: 100%; height: 100vh'></div>
    <div class="eleInfo tttta">
      <div>Longitude: <span id='lng'></span></div>
      <div>Latitude: <span id='lat'></span></div>
      <div>Elevation: <span id='ele'></span></div>
    </div>
  </div>
</template>

<script>
import mapboxgl from "mapbox-gl/dist/mapbox-gl"
import { apiKey } from '../assets/setting'
import MapboxDirections from '@mapbox/mapbox-gl-directions/dist/mapbox-gl-directions'
import axios from "axios"

export default {
  components: {
  },
  data() {
    return {
      accessToken: apiKey,
      mapStyle: 'mapbox://styles/irooori/ckke4h7ki0wtd18qbfqygwew0',
      mapDatas: {
        lat: '37.500618',
        lng: '127.053024'
      }
    };
  },
  mounted() {

    mapboxgl.accessToken = this.accessToken;
    var map = new mapboxgl.Map({
      container: 'map',
      // style: 'mapbox://styles/mapbox/streets-v11',
      // style: 'mapbox://styles/mapbox/light-v10',
      style: this.mapStyle,
      center: [this.mapDatas.lng,this.mapDatas.lat],
      zoom: 16
    })

    // var directions = new MapboxDirections({
    //   accessToken: mapboxgl.accessToken,
    //   unit: 'metric',
    //   profile: 'mapbox/cycling'
    // });
    var directions = new MapboxDirections({
      accessToken: mapboxgl.accessToken,
      unit: 'metric', // Use the metric system to display distances.
      profile: 'mapbox/cycling', // Set the initial profile to walking.
      container: 'map', // Specify an element thats not the map container.
      // proximity: [-79.45, 43.65] // Give search results closer to these coordinates higher priority.
    });

    map.addControl(
        directions,
        'top-left'
    );

    var $this= this;
    map.on('click', function(e) {
      $this.getElevation(e.lngLat.lng + ', ' + e.lngLat.lat);
    });

    map.on('load', function() {
      directions.setOrigin('천호'); // On load, set the origin to "Toronto, Ontario".
      directions.setDestination('화악산'); // On load, set the destination to "Montreal, Quebec".
    });
    directions.on('route', function(e) {
      console.log(e.route); // Logs the current route shown in the interface.
    });

    // create the popup
    // var popup = new mapboxgl.Popup({ offset: 25 }).setText(
    //     'Construction on the Washington Monument began in 1848.'
    // );

// create DOM element for the marker
//     var el = document.createElement('div');
//     el.id = 'marker';

// create the marker
//     new mapboxgl.Marker(el)
//         .setLngLat([127.054041,37.500339])
//         .setPopup(popup) // sets a popup on this marker
//         .addTo(map);
  },
  methods: {
    getElevation(lng,lat) {
      var lngDisplay = document.getElementById('lng');
      var latDisplay = document.getElementById('lat');
      var eleDisplay = document.getElementById('ele');

      // Make the API request
      var query = 'https://api.mapbox.com/v4/mapbox.mapbox-terrain-v2/tilequery/' + lng + ',' + lat + '.json?layers=contour&limit=50&access_token=' + mapboxgl.accessToken;
      axios.get(query).then(function(data) {
        data = data.data;
        // Display the longitude and latitude values
        lngDisplay.textContent = lng;
        latDisplay.textContent = lat;
        // Get all the returned features
        var allFeatures = data.features;
        // Create an empty array to add elevation data to
        var elevations = [];
        // For each returned feature, add elevation data to the elevations array
        for (var i = 0; i < allFeatures.length; i++) {
          elevations.push(allFeatures[i].properties.ele);
        }
        // In the elevations array, find the largest value
        var highestElevation = Math.max(...elevations);
        // Display the largest elevation value
        eleDisplay.textContent = highestElevation + ' meters';
      });
    }
  }
};
</script>

<style>
@import "~mapbox-gl/dist/mapbox-gl.css";
@import "~@mapbox/mapbox-gl-directions/dist/mapbox-gl-directions.css";
#map {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
}

#marker {
  background-image: url('https://docs.mapbox.com/mapbox-gl-js/assets/washington-monument.jpg');
  background-size: cover;
  width: 50px;
  height: 50px;
  border-radius: 50%;
  cursor: pointer;
}

.mapboxgl-popup {
  max-width: 200px;
}

body {
  margin: 0;
  padding: 0;
}

#map {
  position: absolute;
  top: 0;
  bottom: 0;
  width: 100%;
}

.eleInfo {
  position: absolute;
  font-family: sans-serif;
  margin-top: 5px;
  margin-left: 5px;
  padding: 5px;
  width: 200px;
  border: 2px solid black;
  font-size: 20px;
  color: #222;
  background-color: #fff;
  bottom: 100px;
}
</style>

<!--ue warn]: Error in mounted hook: "Error: Invalid LngLat latitude value: must be between -90 and 90"-->

<!--found in-->

<!-- -&ndash;&gt; <HelloWorld> at src/components/HelloWorld.vue-->
<!--<App> at src/App.vue-->