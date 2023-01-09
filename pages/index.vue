<template>
  <l-map style="height: 100vh" :zoom="zoom" :center="defaultLocation">
    <l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
    <l-marker :lat-lng="defaultLocation"></l-marker>
    <l-marker :lat-lng="user.location" :icon="user.icon"> ></l-marker>
  </l-map>
</template>

<script>
import { latLng, icon } from "leaflet";
import userIcon from "~/assets/icons/user.png";

export default {
  data() {
    return {
      defaultLocation: latLng(42.887063, 74.637918),
      user: {
        location: latLng(42.887063, 74.637918),
        icon: icon({
          iconUrl: userIcon,
          iconSize: [32, 32],
        }),
      },
      zoom: 20,
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



<!-- 
  


  

  
<template>
  <l-map style="height: 100vh" :zoom="zoom" :center="defaultLocation">
    <l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
    <l-marker :lat-lng="defaultLocation"></l-marker>
    <l-marker :lat-lng="user.location" :icon="user.icon"> ></l-marker>
  </l-map>
</template>

<script>
import L from "leaflet";
import "leaflet-marker-move";
import { latLng, icon } from "leaflet";
import userIcon from "~/assets/icons/user.png";

export default {
  data() {
    return {
      defaultLocation: latLng(42.887063, 74.637918),
      user: {
        location: latLng(42.887063, 74.637918),
        icon: L.Marker.move(latLng(42.887063, 74.637918), {
          icon: icon({
            iconUrl: userIcon,
            iconSize: [32, 32],
          }),
        }),
      },
      zoom: 20,
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
  watch: {
    "user.location": {
      handler(newValue, oldValue) {
        this.user.icon.move(newValue);
      },
      deep: true,
    },
  },
};
</script>

 -->
