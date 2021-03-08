<template>
  <div class="container d-flex flex-row flex-column align-items-center">
    <h2 class="mb-5">Corona Statistic</h2>

    <div class="d-flex flex-wrap justify-content-around">
      <select
      class="custom-select col-md-7 mb-4 "
      v-model="selectedCountry"
      ref="select"
      @change="getDataByCountry"
    >
      <option selected value="">Global</option>
      <option
        v-for="country in countries"
        :value="country.Slug"
        :key="country.Slug"
        >{{ country.Country }} </option
      >
    </select>
    <input
      type="text"
      placeholder="Search"
      class="form-control col-md-4  "
      @keyup.enter="search()"
      v-model="searchedCountry"
    />
    </div>
    

    <div class=" row mt-5 container ">
      <div class="boxC box  bg-primary mt-2 mx-auto pt-4 col-md-3 ">
        Confirmed <br />{{ !searchedCountry || selectedCountry? totalConfirmed:filterCountry.TotalConfirmed}} 
      </div>
      <div class="boxD box  bg-danger mt-2 mx-auto pt-4 col-md-3">
        Deaths <br />{{ !searchedCountry || selectedCountry? totalDeaths:filterCountry.TotalDeaths }}
      </div>
      <div class="boxR box  bg-success mt-2 mx-auto pt-4 col-md-3 ">
        Recovered <br />{{ !searchedCountry || selectedCountry? totalRecovered:filterCountry.TotalRecovered }}
      </div>
    </div>

    <div class="row mt-3 container">
      <div class="boxC box  bg-primary mt-2 mx-auto pt-4 col-md-3 ">
        New Confirmed <br />{{  !searchedCountry || selectedCountry? NewConfirmed:filterCountry.NewConfirmed }}
      </div>
      <div class="boxD box  bg-danger mt-2 mx-auto pt-4 col-md-3">
        New Deaths <br />{{ !searchedCountry || selectedCountry? NewDeaths:filterCountry.NewDeaths}}
      </div>
      <div class="boxR box  bg-success mt-2 mx-auto pt-4 col-md-3 ">
        New Recovered <br />{{ !searchedCountry || selectedCountry? NewRecovered:filterCountry.NewRecovered }}
      </div>
    </div>

    <table class="table table-bordered table-responsive-md table-dark mt-5 ">
      <thead>
        <tr>
          <th>#</th>
          <th>Country</th>
          <th>Confirmed</th>
          <th>Deaths</th>
          <th>Recovered</th>
          <th>New Confirmed</th>
          <th>New Deaths</th>
          <th>New Recovered</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(country, index) in allCountries" :key="country.Slug">
          <th>{{ index + 1 }}</th>
          <td>{{ country.Country }}</td>
          <td>{{ country.TotalConfirmed }}</td>
          <td>{{ country.TotalDeaths }}</td>
          <td>{{ country.TotalRecovered }}</td>
          <td>{{ country.NewConfirmed }}</td>
          <td>{{ country.NewDeaths }}</td>
          <td>{{ country.NewRecovered }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      countries: [],
      totalConfirmed: 0,
      totalDeaths: 0,
      totalRecovered: 0,
      NewConfirmed: 0,
      NewDeaths: 0,
      NewRecovered: 0,
      allCountries: {},
      selectedCountry: "",
      searchedCountry: "",
      filterCountry:''
    };
  },
  computed: {},
  filters: {
    numberFormat(number) {
      return number.toLocaleString();
    },
  },
  methods: {
    getCountries() {
      axios.get("https://api.covid19api.com/countries").then((res) => {
        this.countries = res.data;
      });
      console.log();
    },

    async getGlobalData() {
      await axios.get("https://api.covid19api.com/summary").then((response) => {
        const data = response.data;
        this.totalConfirmed = response.data.Global.TotalConfirmed;
        this.totalDeaths = response.data.Global.TotalDeaths;
        this.totalRecovered = response.data.Global.TotalRecovered;

        this.NewConfirmed = response.data.Global.NewConfirmed;
        this.NewDeaths = response.data.Global.NewDeaths;
        this.NewRecovered = response.data.Global.NewRecovered;

        this.allCountries = response.data.Countries;
      });
    },
    async search() {
      await axios.get("https://api.covid19api.com/summary").then(() => {
        this.filterCountry = this.allCountries.find((country) => {
          return country.Country.includes(this.searchedCountry.charAt(0).toUpperCase() + this.searchedCountry.slice(1));
        })

        console.log(this.filterCountry);
      })
       this.selectedCountry=""
      //  this.searchedCountry=''
      console.log(this.filterCountry)
    },

    getDataByCountry() {
      const data = this.$data.allCountries.filter((country) => {
        return country.Slug == this.selectedCountry;
      });
      this.totalConfirmed = data[0].TotalConfirmed;
      this.totalDeaths = data[0].TotalDeaths;
      this.totalRecovered = data[0].TotalRecovered;

      this.NewConfirmed = data[0].NewConfirmed;
      this.NewDeaths = data[0].NewDeaths;
      this.NewRecovered = data[0].NewRecovered;
    },
  },
  mounted() {
    this.getCountries(), this.getGlobalData();
    console.log(this.searchedCountry);
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  /* color: #2c3e50; */
  margin-top: 60px;
}
.box {
  height: 100px !important;
  color: aliceblue;
  font-weight: bold;
}

h2 {
  color: white;
  font-weight: bolder;
}
.bg-primary {
  background-color: #0652DD !important;
}
.bg-danger {
  background-color: #c44569 !important;
}
.bg-success {
  background-color: #218c74 !important;
}
.box{
  border-radius: 10px;
}
.table-dark{
  background-color: #706fd3  !important;
}
</style>
