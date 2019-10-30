<template>
  <main>
    <l-map :bounds="bounds" :options="mapOptions">
      <l-tile-layer :url="url" :attribution="attribution" />
      <card v-for="request in requests" :key="request.originalObj.request_id" :request="request" />
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
          originalObj: {
            request_id: "23235nsd"
          },
          time: 1572301996671,
          location: latLng(12.24633, 91.92501),
          brand: "talabat",
          count: 4,
          total: 20,
          platform: "android"
        }
      ],
      bounds: [[10.1, 140.2], [10.4, 45.3]]
    };
  },
  methods: {
    calculateBounds(requests) {
      const [west, south, east, north] = bbox({
        type: "FeatureCollection",
        features: requests
          .map(r =>
            bbox({
              type: "Point",
              coordinates: [r.location.lng, r.location.lat]
            })
          )
          .map(box => bboxPolygon(box))
      });

      return [[south, west], [north, east]];
    },
    visibleOnMap(requests) {
      const minX = this.bounds[0][1];
      const minY = this.bounds[0][0];
      const maxX = this.bounds[1][1];
      const maxY = this.bounds[1][0];

      return requests.every(
        r =>
          minY <= r.location.lat &&
          r.location.lat <= maxY &&
          minX <= r.location.lng &&
          r.location.lng <= maxX
      );
    }
  },
  sockets: {
    connect() {
      console.log("Socket connected");
    },
    swimlanes(msg) {
      const now = Date.now();

      const newRequest = {
        ...msg,
        count: msg.originalObj.swimlanes.filter(s => s.restaurant_ids.length)
          .length,
        total: msg.count,
        time: now
      };

      const newRequests = [
        ...this.requests.filter(req => now - req.time < 1500),
        newRequest
      ];

      if (!this.visibleOnMap(newRequests)) {
        this.bounds = this.calculateBounds(newRequests);
      }

      this.requests = newRequests;
    }
  }
};
</script>

<style>
@import url("https://fonts.googleapis.com/css?family=Righteous&display=swap");

main {
  position: absolute;
  width: 100%;
  height: 100%;
  margin: -8px;
}

* {
  font-family: "Righteous", cursive;
}
</style>