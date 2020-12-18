<template>
  <div class="main text-center bg-image">
    <div class="mask h-100">
      <div class="d-flex justify-content-center align-items-center h-100">
        <div class="text-white">
          <h2 class="mb-3">Find food trucks in San Francisco</h2>
          <input
            type="checkbox"
            id="closest"
            value="closest"
            v-model="filters"
            :disabled="proximityDisabled"
          />
          <label for="open">Closest to me</label>
          <input type="checkbox" id="open" value="open" v-model="filters" />
          <label for="open">Open right now</label>
          <br />
          <button class="search" v-on:click="search">Search</button>
          <div v-if="error">
            <mdb-alert color="danger">
              Search call failed, please try again!
            </mdb-alert>
          </div>
        </div>
      </div>
    </div>
    <div id="foodTrucks">
      <div v-if="foodTrucks.length > 0">
        <mdb-tbl responsive bordered hover>
          <mdb-tbl-head color="primary-color" textWhite>
            <tr>
              <th>Food Truck</th>
              <th>Description</th>
              <th>Hours</th>
              <th>Location</th>
            </tr>
          </mdb-tbl-head>

          <mdb-tbl-body>
            <tr
              scope="row"
              v-for="(foodTruck, index) in foodTrucks"
              :key="index"
            >
              <td>{{ foodTruck.applicant }}</td>
              <td>{{ foodTruck.optionaltext }}</td>
              <td>{{ foodTruck.starttime }} - {{ foodTruck.endtime }}</td>
              <td>
                <a
                  target="_blank"
                  :href="
                    `https://maps.google.com/?q=${foodTruck.latitude},${foodTruck.longitude}`
                  "
                  >{{ foodTruck.location }}</a
                >
              </td>
            </tr>
          </mdb-tbl-body>
        </mdb-tbl>
      </div>
    </div>
  </div>
</template>

<script>
import { mdbTbl, mdbTblHead, mdbTblBody, mdbAlert } from "mdbvue";
export default {
  name: "Main",
  components: {
    mdbTbl,
    mdbTblHead,
    mdbTblBody,
    mdbAlert,
  },
  data() {
    return {
      latitude: null,
      longitude: null,
      proximityDisabled: true,
      filters: [],
      foodTrucks: [],
      error: false,
    };
  },
  methods: {
    getCoordinates: function() {
      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition((position) => {
          this.latitude = position.coords.latitude;
          this.longitude = position.coords.longitude;
          this.proximityDisabled = false;
        });
      } else {
        console.log("Geolocation is not supported by this browser.");
      }
    },
    haversineDistance: function([lat1, lon1], [lat2, lon2]) {
      // Math lib function names
      const [pi, asin, sin, cos, sqrt, pow, round] = [
          "PI",
          "asin",
          "sin",
          "cos",
          "sqrt",
          "pow",
          "round",
        ].map((k) => Math[k]),
        // degrees as radians
        [rlat1, rlat2, rlon1, rlon2] = [lat1, lat2, lon1, lon2].map(
          (x) => (x / 180) * pi
        ),
        dLat = rlat2 - rlat1,
        dLon = rlon2 - rlon1,
        radius = 6372.8; // km

      // km
      return (
        round(
          radius *
            2 *
            asin(
              sqrt(
                pow(sin(dLat / 2), 2) +
                  pow(sin(dLon / 2), 2) * cos(rlat1) * cos(rlat2)
              )
            ) *
            100
        ) / 100
      );
    },
    async search() {
      let url = `https://data.sfgov.org/resource/jjew-r69b.json?$select=distinct location,applicant,latitude,longitude,optionaltext,starttime,endtime&$$app_token=vY15loVzkeBMOlCMoWtCuFsOm`;

      if (this.filters.includes("open")) {
        const date = new Date();
        const time = date.toLocaleTimeString("en-US", {
          hour: "2-digit",
          minute: "2-digit",
          hour12: false,
        });
        url += `&$where=start24 <= '${time}' and end24 > '${time}'`;
      }

      let response;
      this.error = false;
      await fetch(url)
        .then((resp) => resp.json())
        .then((data) => (response = data))
        .catch(() => {
          this.error = true;
        });

      if (this.filters.includes("closest")) {
        response.forEach((foodTruck) => {
          foodTruck.distance = this.haversineDistance(
            [this.latitude, this.longitude],
            [foodTruck.latitude, foodTruck.longitude]
          );
        });
        response.sort((a, b) => {
          return a.distance - b.distance;
        });
      }
      this.foodTrucks = response;
      //TODO: control through lifecycle method
      setTimeout(() => {
        let table = document.getElementById("foodTrucks");
        table.scrollIntoView({ behavior: "smooth" });
      }, 0);
    },
  },
  created() {
    this.getCoordinates();
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.main {
  height: 100vh;
  background-image: url("../assets/foodTruck.jpg");
  background-size: contain;

  @media only screen and (min-width: 905px) {
    background-repeat: no-repeat;
    background-size: cover;
    background-position-x: center;
  }
}

.mask {
  background-color: rgba(0, 0, 0, 0.7);
}

#open {
  margin-left: 0.5rem;
}

.search {
  background-color: #4285f4; /* Green */
  border: none;
  color: white;
  margin: 0.5rem 0;
  padding: 1rem 2rem;
  border-radius: 0.2rem;
}

input[type="checkbox"] {
  margin-right: 0.25rem;
}

a {
  text-decoration: underline;
}
</style>
