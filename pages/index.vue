<template>
  <v-row>
    <v-col>
      <l-map
        style="height: 100vh"
        :min-zoom="minZoom"
        :zoom="zoom"
        :center="user.location"
        :options="{ zoomControl: false }"
        @contextmenu="
          (e) => {
            isDialogOpen = true;
            selectedPoint = e.latlng;
          }
        "
      >
        <l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
        <l-control-zoom position="bottomright"></l-control-zoom>
        <l-moving-marker
          ref="userAniMarker"
          :lat-lng="user.location"
          :icon="user.icon"
          :duration="2000"
          :options="{ pan: false }"
        ></l-moving-marker>

        <l-marker
          v-for="(point, index) in points"
          :key="index"
          :lat-lng="point.latlng"
        ></l-marker>
      </l-map>

      <v-dialog
        style="z-index: 10000"
        v-model="isDialogOpen"
        persistent
        max-width="600px"
      >
        <add-new-point
          :closeDialog="() => (isDialogOpen = false)"
          :createPoint="createPoint"
        />
      </v-dialog>
    </v-col>
  </v-row>
</template>

<script>
import { latLng, icon } from "leaflet";
import userIcon from "~/assets/icons/user.png";
import LMovingMarker from "vue2-leaflet-movingmarker";
import AddNewPoint from "../components/add-new-point.vue";

export default {
  components: {
    LMovingMarker,
    AddNewPoint,
  },
  data() {
    return {
      points: [],
      isDialogOpen: false,
      selectedPoint: null,
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
  async created() {
    this.fetchPoints();
  },
  async mounted() {
    this.watchId = navigator.geolocation.watchPosition((pos) => {
      this.user.location = latLng(pos.coords.latitude, pos.coords.longitude);
    });
  },
  beforeDestroy() {
    navigator.geolocation.clearWatch(this.watchId);
  },
  methods: {
    async fetchPoints() {
      const snapshot = await this.$fire.firestore.collection("points").get();
      snapshot.docs.forEach((snapDoc) => {
        const doc = snapDoc.data();
        const geo = doc.latlng;

        const pointObject = {
          name: doc.name,
          latlng: latLng(geo.latitude, geo.longitude),
        };

        this.points.push(pointObject);
      });
    },
    async createPoint({ pointName }) {
      const pointGeo = {
        latitude: this.selectedPoint.lat,
        longitude: this.selectedPoint.lng,
      };

      const pointObject = {
        name: pointName,
        latlng: pointGeo,
      };

      try {
        await this.$fire.firestore.collection("points").add(pointObject);
        this.points.push({
          name: pointName,
          latlng: latLng(pointGeo.latitude, pointGeo.longitude),
        });
      } catch (e) {
        console.error(`Firebase error: ${e}`);
        alert(`Firebase error: ${e}`);
      }
    },
  },
};
</script>


  

