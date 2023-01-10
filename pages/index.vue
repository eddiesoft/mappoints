<template>
  <v-row>
    <v-col>
      <l-map
        style="height: 100vh"
        :min-zoom="minZoom"
        :zoom="zoom"
        :center="user.location"
      >
        <l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
        <l-marker :lat-lng="centerCameraLocation"></l-marker>
        <l-moving-marker
          ref="userAniMarker"
          :lat-lng="user.location"
          :icon="user.icon"
          :duration="2000"
        >
          ></l-moving-marker
        >
      </l-map>
    </v-col>
  </v-row>
</template>

<script>
import LMovingMarker from "vue2-leaflet-movingmarker";
import { latLng, icon } from "leaflet";
import userIcon from "~/assets/icons/user.png";

export default {
  components: {
    LMovingMarker,
  },
  data() {
    return {
      centerCameraLocation: latLng(42.887063, 74.637918),
      user: {
        location: latLng(42.887063, 74.637918),
        icon: icon({
          iconUrl: userIcon,
          iconSize: [32, 32],
        }),
      },
      zoom: 20,
      minZoom: 6,
      attribution:
        '&copy; <a target="_blank" href="http://osm.org/copyright">OpenStreetMap</a> contributors',
      url: "https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png",
    };
  },
  mounted() {
    this.watchId = navigator.geolocation.watchPosition((pos) => {
      this.user.location = latLng(pos.coords.latitude, pos.coords.longitude);
    });
  },
  beforeDestroy() {
    navigator.geolocation.clearWatch(this.watchId);
  },
};
</script>


  

