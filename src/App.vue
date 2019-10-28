<template>
  <main>
    <l-map
      :zoom="zoom"
      :center="center"
      :options="mapOptions"
      @update:center="centerUpdate"
      @update:zoom="zoomUpdate"
    >
      <l-tile-layer :url="url" :attribution="attribution" />
      <div v-for="request in requests" :key="request">
        <card :request="request" />
      </div>
    </l-map>
  </main>
</template>

<script>
import { latLng } from "leaflet";
import { LMap, LTileLayer } from "vue2-leaflet";
import Card from "./components/Card.vue";

export default {
  name: "app",
  components: {
    LMap,
    LTileLayer,
    Card
  },
  data() {
    return {
      zoom: 3,
      center: latLng(43.24633, 76.92501),
      url: "http://{s}.tile.osm.org/{z}/{x}/{y}.png",
      attribution:
        '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors',
      currentZoom: 11.5,
      currentCenter: latLng(43.24633, 76.92501),
      showParagraph: false,
      mapOptions: {
        zoomSnap: 0.5
      },
      requests: [
        {
          location: latLng(43.24633, 76.92501),
          brand: "foodpanda",
          count: 3,
          platform: "android"
        },
        {
          location: latLng(32.24633, 71.92501),
          brand: "foodpanda",
          count: 5,
          platform: "apple"
        },
        {
          location: latLng(12.24633, 91.92501),
          brand: "foodpanda",
          count: 1,
          platform: "firefox"
        }
      ]
    };
  },
  methods: {
    zoomUpdate(zoom) {
      this.currentZoom = zoom;
    },
    centerUpdate(center) {
      this.currentCenter = center;
    }
  }
};
</script>

<style>
main {
  position: absolute;
  width: 100%;
  height: 100%;
  margin: -8px;
  font-family: "Sans Forgetica";
}

@font-face {
  font-family: "Sans Forgetica";
  src: url("./assets/SansForgetica-Regular.otf") format("opentype");
}
* {
  font-family: "Sans Forgetica";
}
</style>