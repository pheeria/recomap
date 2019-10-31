<template>
  <l-marker :lat-lng="request.location">
    <l-tooltip :options="tooltipOptions">
      <div class="card">
        <span
          class="count"
          :class="{alert: request.count === 0, success: request.count === request.total}"
        >{{request.count}}/{{request.total}}</span>
        <img class="brand" :src="resolveImage(request.brand)" :alt="request.brand" />
        <img class="platform" :src="resolveImage(request.platform)" :alt="request.platform" />
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
  data() {
    return {
      tooltipOptions: { permanent: true, interactive: false }
    };
  },
  props: {
    request: {
      type: Object,
      default: function() {
        return {
          location: Object,
          brand: String,
          count: Number,
          total: Number,
          platform: String
        };
      }
    }
  },
  methods: {
    resolveImage: function(image) {
      const images = require.context("../assets/", false, /\.svg$/);
      return images(`./${image}.svg`);
    }
  }
};
</script>

<style scoped>
img {
  width: 16px;
}
.card {
  display: grid;
  grid-template-areas:
    "count count brand"
    "count count platform";
  grid-gap: 10px;
  font-size: 2em;
  font-weight: bolder;
}
.count {
  grid-area: count;
  vertical-align: middle;
}
.brand {
  grid-area: brand;
}
.platform {
  grid: platform;
}
.alert {
  color: #ff0e11;
}
.success {
  color: #00a900;
}
</style>