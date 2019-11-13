<template>
  <main>
    <l-map :bounds="bounds">
      <l-tile-layer :url="url" :attribution="attribution" />
      <card v-for="request in requests" :key="request.id" :request="request" />
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
      requests: [
        {
          id: "23235nsd",
          time: 1572301996671,
          location: latLng(12.24633, 91.92501),
          brand: "talabat",
          country: "in",
          count: 4,
          total: 20,
          platform: "android"
        }
      ],
      bounds: [[10.1, 140.2], [10.4, 45.3]],
      lastRecenter: 1572301996671,
    };
  },
  methods: {
    calculateBounds(requests) {
      const [west, south, east, north] = bbox({
        type: "FeatureCollection",
        features: requests.map(r =>
          bboxPolygon(
            bbox({
              type: "Point",
              coordinates: [r.location.lng, r.location.lat]
            })
          )
        )
      });

      return [[south, west], [north, east]];
    },
    visibleOnMap(request) {
      return (
        this.bounds[0][0] <= request.location.lat &&
        request.location.lat <= this.bounds[1][0] &&
        this.bounds[0][1] <= request.location.lng &&
        request.location.lng <= this.bounds[1][1]
      );
    },
    similar(aRequest, newRequest) {
      return (
        aRequest.country === newRequest.country
        && aRequest.brand === newRequest.brand
        && aRequest.platform === newRequest.platform
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
        time: now
      };

      let newRequests = this.requests
          .filter(req => now - req.time < 1200);

      if (newRequests.some(req => this.similar(req, newRequest))) {
        newRequests = newRequests
          .map(req => this.similar(req, newRequest) ? {
              ...req,
              count: req.count + newRequest.count,
              total: req.total + newRequest.total,
              time: req.time + 265
            }
            : req
          )
      } else {
        newRequests = newRequests
          .concat(newRequest);
      }

      if (now - this.lastRecenter > 10000 && !this.visibleOnMap(newRequest)) {
        this.bounds = this.calculateBounds(newRequests);
        this.lastRecenter = now;
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