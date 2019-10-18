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
      <l-marker :lat-lng="withTooltip">
        <l-tooltip :options="{ permanent: true, interactive: true }">
          <div @click="innerClick">Сәлем, Әлем!</div>
        </l-tooltip>
      </l-marker>
    </l-map>
  </main>
</template>

<script>
import { latLng } from "leaflet";
import { LMap, LTileLayer, LMarker, LTooltip } from "vue2-leaflet";
export default {
  name: "app",
  components: {
    LMap,
    LTileLayer,
    LMarker,
    LTooltip
  },
  data() {
    return {
      zoom: 3,
      center: latLng(43.24633, 76.92501),
      url: "http://{s}.tile.osm.org/{z}/{x}/{y}.png",
      attribution:
        '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors',
      withTooltip: latLng(43.24633, 76.92501),
      currentZoom: 11.5,
      currentCenter: latLng(43.24633, 76.92501),
      showParagraph: false,
      mapOptions: {
        zoomSnap: 0.5
      }
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

<style scoped>
main {
  position: absolute;
  width: 100%;
  height: 100%;
  margin: -8px;
}
</style>