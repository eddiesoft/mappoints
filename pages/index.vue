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
            isCreatePointDialogOpen = true;
            createdPointLatLng = e.latlng;
          }
        "
      >
        <l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
        <l-moving-marker
          ref="userAniMarker"
          :lat-lng="user.location"
          :icon="user.icon"
          :duration="2000"
          :options="{ pan: false }"
        ></l-moving-marker>

        <l-marker
          v-for="point in points"
          :key="point.id"
          :lat-lng="point.latlng"
          @click="() => viewPointDetails(point)"
        ></l-marker>
      </l-map>

      <v-dialog
        style="z-index: 10000"
        v-model="isCreatePointDialogOpen"
        persistent
        max-width="600px"
      >
        <add-new-point
          :closeDialog="() => (isCreatePointDialogOpen = false)"
          :createPoint="createPoint"
        />
      </v-dialog>
      <v-dialog
        style="z-index: 10000"
        v-model="isViewPointDetailsDialogOpen"
        persistent
        max-width="600px"
      >
        <view-point-details
          :closeDialog="() => (isViewPointDetailsDialogOpen = false)"
          :selectedPoint="selectedPoint"
          :deletePoint="deletePoint"
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
import ViewPointDetails from "../components/view-point-details.vue";

export default {
  components: {
    LMovingMarker,
    AddNewPoint,
    ViewPointDetails,
  },
  data() {
    return {
      isCreatePointDialogOpen: false,
      isViewPointDetailsDialogOpen: false,
      points: [],
      createdPointLatLng: null,
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
          id: snapDoc.id,
          name: doc.name,
          latlng: latLng(geo.latitude, geo.longitude),
        };

        this.points.push(pointObject);
      });
    },
    async createPoint({ pointName }) {
      const pointGeo = {
        latitude: this.createdPointLatLng.lat,
        longitude: this.createdPointLatLng.lng,
      };

      const pointObject = {
        name: pointName,
        latlng: pointGeo,
      };

      try {
        const point = await this.$fire.firestore
          .collection("points")
          .add(pointObject);
        this.points.push({
          id: point.id,
          name: pointName,
          latlng: latLng(pointGeo.latitude, pointGeo.longitude),
        });
      } catch (e) {
        console.error(`Firebase error: ${e}`);
        alert(`Firebase error: ${e}`);
      }
    },
    async deletePoint(pointId) {
      try {
        await this.$fire.firestore.collection("points").doc(pointId).delete();
        this.points = this.points.filter((e) => e.id != pointId);
        this.isViewPointDetailsDialogOpen = false;
      } catch (e) {
        console.error(`Firebase error: ${e}`);
        alert(`Firebase error: ${e}`);
      }
    },
    viewPointDetails(point) {
      this.selectedPoint = point;
      this.isViewPointDetailsDialogOpen = true;
    },
  },
};
</script>


  

