<template>
  <div id="sub-nav">
    <!-- Title -->
    <div id="sub-nav-title">
      <h3>Rechercher et ajouter un vélo</h3>
      <img src="@/assets/map_bike.png" alt="vélo" style="height: 25px;">
    </div>
    <!-- Places input -->
    <gmap-autocomplete :options="{fields: ['geometry']}"
                        @input="place = $event.target.value"
                        @place_changed="setPlace"
                        style="margin-right: 10px;">
    </gmap-autocomplete>
    <!-- Btn add bike -->
    <v-btn id="add-btn" :disabled="! place" depressed rounded
           @click="addBike()">
      <span>Ajouter un vélo</span>
      <img src="@/assets/map_bike.png" alt="vélo" style="height: 25px;">
    </v-btn>
  </div>
</template>

<style>
  #sub-nav {
    margin-left: 20px;
    margin-top: 5px;
    margin-bottom: 15px;
  }
  #sub-nav-title {
    display: flex;
    margin-bottom: 5px;
  }
  #add-btn {
    background-color: #331C54;
    color: white;
    height: 25px;
  }
</style>

<script>
  import ApiSrv from '@/js/services/ApiSrv';

  export default {
    name: "SubNavigation",

    data() {
      return {
        place: '',
        selectedPlace: null
      };
    },
    methods: {
      /**
       * Receives place via the autocomplete component
       * @param {Object} place selected place
       */
      setPlace(place) {
        this.selectedPlace = place;
      },
      /**
       * Create new bike and show it on the map
       */
      addBike() {
        if (this.selectedPlace) {
          const longitude = this.selectedPlace.geometry.location.lng();
          const latitude = this.selectedPlace.geometry.location.lat();
          const newBike = {
            serial_number: "",
            location: { type: "Point", coordinates: [longitude, latitude] },
            position: { lng: longitude, lat: latitude },
            in_order: true,
            service_status: 1,
            battery_level: 100
          };

          // Call api server create bike
          ApiSrv.addBike(newBike).then((response) => {
            if (response.ok) {
              this.$emit('updateMap');
              this.$emit('setMapCenter', newBike);
              this.$emit('sendUserAction', 'add');
            }
          }).catch((message) => {
            console.log(message);
          });

          this.selectedPlace = null;
        }
      },
    },
  };
</script>
