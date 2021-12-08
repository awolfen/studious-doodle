<template>
  <FormInput @add-marker="addMarkerToMap" @add-existing="addExistingCodes" />
  <div id="map"></div>
</template>

<script>
import "leaflet/dist/leaflet.css";
import L from "leaflet";
import markerIcon from "../../node_modules/leaflet/dist/images/marker-icon.png";
import FormInput from "./FormInput.vue";
export default {
  name: "Map",
  components: { FormInput },
  data() {
    return {
      map: Object,
      initialMapState: {
        coords: [51.505, -0.09],
        zoom: 12,
      },
    };
  },
  methods: {
    setMap() {
      this.map = L.map("map").setView(
        this.initialMapState.coords,
        this.initialMapState.zoom
      );
      L.tileLayer(
        "https://api.mapbox.com/styles/v1/{id}/tiles/{z}/{x}/{y}?access_token={accessToken}",
        {
          attribution:
            'Map data &copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors, Imagery © <a href="https://www.mapbox.com/">Mapbox</a>',
          maxZoom: 18,
          id: "mapbox/streets-v11",
          tileSize: 512,
          zoomOffset: -1,
          accessToken:
            "pk.eyJ1IjoiYXdvbGZlbiIsImEiOiJja3d3YjI3cG0wMjQyMnhsYTFrZHhsOGdpIn0.Mb2nXi437Z60nEyT2C6FUA",
        }
      ).addTo(this.map);
      //marker icon not showing by default, solution found here https://stackoverflow.com/questions/60174040/marker-icon-isnt-showing-in-leaflet
      L.Marker.prototype.setIcon(
        L.icon({
          iconUrl: markerIcon,
        })
      );
    },
    addMarkerToMap(coords) {
      this.map.setView(coords);
      L.marker(coords).addTo(this.map);
    },
    addMarkerToMapNoMove(coords) {
      L.marker(coords).addTo(this.map);
    },
    addExistingCodes(coordsList) {
      coordsList.forEach((coord) => this.addMarkerToMapNoMove(coord));
    },
  },
  mounted() {
    this.setMap();
  },
};
</script>

<style>
#map {
  height: 45vh;
}
</style>
