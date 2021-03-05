<template>
  <div class="container ">
      <select class="custom-select" v-model="selectedCountry"  @change="getDataByCountry">
        <option selected disabled value="">Global</option>
        <option v-for="country in countries" :value="country.Slug" :key="country.Slug">{{country.Country}}</option>
      </select>
      <div class="d-flex justify-content-center mt-5">
        <div class="boxC box mx-auto bg-primary w-25 pt-4 ">Confirmed <br>{{totalConfirmed }}</div>
        <div class="boxD box mx-auto bg-danger w-25 pt-4">Deaths <br>{{totalDeaths}}</div>
        <div class="boxR box mx-auto bg-success w-25 pt-4 ">Recovered <br>{{totalRecovered}}</div>
      </div>
      <table class="table table-bordered table-dark mt-5">
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
    getGlobalData(){
      axios.get('https://api.covid19api.com/summary')
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
      
      console.log(data)
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
  background-color: khaki;
}
.box{
  height: 100px  !important;
  color: aliceblue;
  font-weight: bold;
}
</style>
