<template>
  <main>
    <l-map :bounds="bounds" :options="mapOptions">
      <l-tile-layer :url="url" :attribution="attribution" />
      <div v-for="request in requests" :key="request.time">
        <card :request="request" />
      </div>
    </l-map>
  </main>
</template>

<script>
import { latLng } from "leaflet";
import { LMap, LTileLayer } from "vue2-leaflet";
import Card from "./components/Card.vue";
import bbox from "@turf/bbox";
import bboxPolygon from "@turf/bbox-polygon";

export default {
  name: "app",
  components: {
    LMap,
    LTileLayer,
    Card
  },
  data() {
    return {
      url: "https://{s}.tile.osm.org/{z}/{x}/{y}.png",
      attribution:
        '&copy; <a href="https://osm.org/copyright">OpenStreetMap</a> contributors',
      mapOptions: {
        zoomSnap: 0.5
      },
      requests: [
        {
          time: 1572301996671,
          location: latLng(12.24633, 91.92501),
          brand: "pedidosya",
          count: 1,
          platform: "web"
        }
      ]
    };
  },
  computed: {
    bounds() {
      let [west, south, east, north] = bbox({
        type: "FeatureCollection",
        features: this.requests
          .map(r =>
            bbox({
              type: "Point",
              coordinates: [r.location.lng, r.location.lat]
            })
          )
          .map(box => bboxPolygon(box))
      });

      // If bounding box represents a dot, we need to spread it out a bit
      if (west === east) {
        west--;
        east++;
      }
      if (south === north) {
        south--;
        north++;
      }

      return [[south, west], [north, east]];
    }
  },
  sockets: {
    connect() {
      console.log("Socket connected");
    },
    swimlanes(msg) {
      const now = Date.now();

      const newRequest = {
        time: now,
        ...msg
      };

      this.requests = [
        ...this.requests.filter(req => now - req.time < 60000),
        newRequest
      ];
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