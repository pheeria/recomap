<template>
  <l-marker :lat-lng="request.location">
    <l-tooltip :options="{ permanent: true, interactive: true }">
      <div class="card">
        <img :src="resolveImage(request.brand)" :alt="request.brand" />
        <span>#{{request.count}}</span>
        <img :src="resolveImage(request.platform)" :alt="request.platform" />
      </div>
    </l-tooltip>
  </l-marker>
</template>

<script>
import { LMarker, LTooltip } from "vue2-leaflet";

export default {
  components: {
    LMarker,
    LTooltip
  },
  props: {
    request: {
      type: Object,
      default: function() {
        return {
          location: Object,
          brand: String,
          count: Number,
          platform: String
        };
      }
    }
  },
  methods: {
    resolveImage: function(image) {
      let images = require.context("../assets/", false, /\.png$|\.jpg$|\.svg$/);
      return images(`./${image}.svg`);
    }
  }
};
</script>

<style scoped>
img {
  width: 42px;
}
.card {
  display: flex;
  flex-direction: row;
  font-size: 2em;
}
</style>