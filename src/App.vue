<template>
  <div class="container d-flex flex-row flex-column align-items-center">
      <h2 class="mb-5">Corona Statistic</h2>
      <select class="custom-select" v-model="selectedCountry" ref="select"  @change="getDataByCountry">
        <option selected  value="" >Global</option>
        <option v-for="country in countries" :value="country.Slug" :key="country.Slug">{{country.Country}}</option>
      </select>
      <div class=" row mt-5 container ">
        <div class="boxC box  bg-primary mt-2 mx-auto pt-4 col-md-3 ">Confirmed <br>{{totalConfirmed }}</div>
        <div class="boxD box  bg-danger mt-2 mx-auto pt-4 col-md-3">Deaths <br>{{totalDeaths}}</div>
        <div class="boxR box  bg-success mt-2 mx-auto pt-4 col-md-3 ">Recovered <br>{{totalRecovered}}</div>
      </div>
      <table class="table table-bordered table-responsive-md table-dark mt-5 ">
        <thead>
          <tr>
            <th >#</th>
            <th >Country</th>
            <th >Confirmed</th>
            <th >Deaths</th>
            <th >Recovered</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(country,index) in allCountries" :key="country.Slug">
            <th >{{index+1}}</th>
            <td>{{country.Country}}</td>
            <td>{{country.TotalConfirmed}}</td>
            <td>{{country.TotalDeaths}}</td>
            <td>{{country.TotalRecovered}}</td>
          </tr>
        </tbody>
      </table>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  data(){
    return{
      countries:{},
      totalConfirmed:0,
      totalDeaths:0,
      totalRecovered:0,
      allCountries:{},
      selectedCountry:""
    }
  },
  filters:{
    numberFormat(number){
      return number.toLocaleString();
    }
  },
  methods:{
    getCountries(){
      axios.get('https://api.covid19api.com/countries')
        .then(res=>{
          this.countries=res.data
        })
    },
    async getGlobalData(){
     await axios.get('https://api.covid19api.com/summary')
        .then(response=>{
          const data=response.data;
          this.totalConfirmed=response.data.Global.TotalConfirmed
          this.totalDeaths=response.data.Global.TotalDeaths
          this.totalRecovered=response.data.Global.TotalRecovered
          this.allCountries=response.data.Countries
        })
    },
    getDataByCountry(){
      const data=this.$data.allCountries.filter(country=>{
          return country.Slug==this.selectedCountry
      })
          this.totalConfirmed=data[0].TotalConfirmed
          this.totalDeaths=data[0].TotalDeaths
          this.totalRecovered=data[0].TotalRecovered

     
    }
  },
  mounted(){
    this.getCountries(),
    this.getGlobalData()
  }
}
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
.box{
  height: 100px  !important;
  color: aliceblue;
  font-weight: bold;
}

h2{
  color: rgb(78, 99, 99);
  font-weight: bolder;

}
</style>
