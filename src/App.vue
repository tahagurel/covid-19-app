<template>
  <div class="mainPage">
    <h1 class="pageHeader">Covid-19 Global Statistics </h1>
    <div class="globalInfo">
      <div class="boxStyle">
        <div>Confirmed</div>
        <div class="apiGlobalData">{{confirmed | numberFormat()}}</div>
      </div>
      <div class="boxStyle">
        <div>Deaths</div>
        <div class="apiGlobalData">{{deaths | numberFormat()}}</div>
      </div>
      <div class="boxStyle">
        <div>Recovered</div>
        <div class="apiGlobalData">{{recovered | numberFormat()}}</div>
      </div>
    </div>
    <div class="selectWrapper">
      <select v-model="selectedCountry" class="select" @change="getDataByCountry">
        <option value="">Global</option>
        <option :key="key" :value="item.Slug" v-for="(item,key) in countries">
          {{item.Country}}
        </option>
      </select>
    </div>
    <table>
      <tr class="tableHeaders">
        <th scope="col">#</th>
        <th scope="col">Country</th>
        <th scope="col">Confirmed</th>
        <th scope="col">Deaths</th>
        <th scope="col">Recovered</th>
      </tr>
      <tr :key="index" v-for="(item,index) in data.Countries " class="tableItems">
        <th scope="row">{{index}}</th>
        <td>{{item.Country}}</td>
        <td>{{item.TotalConfirmed}}</td>
        <td>{{item.TotalDeaths}}</td>
        <td>{{item.TotalRecovered}}</td>
      </tr>
    </table>
  </div>
</template>

<script>
  import axios from 'axios'
  export default {
    data() {
      return {
        selectedCountry: '',
        countries: {},
        confirmed: 0,
        data: {},
        deaths: 0,
        recovered: 0
      }
    },
    filters: {
      numberFormat(number) {
        return number.toLocaleString();
      }
    },
    methods: {
      getCountries() {
        axios.get('https://api.covid19api.com/countries')
          .then(response => {
            this.countries = response.data
            this.countries.sort(function (a, b) {
              if (a.Country < b.Country) {return -1;}
              if (a.Country > b.Country) {return 1;}
              return 0;
            })
          })
      },
      getGlobalDatas() {
        axios.get('https://api.covid19api.com/summary')
          .then(response => {
            let data = response.data
            this.data = response.data;
            this.confirmed = data.Global.TotalConfirmed;
            this.deaths = data.Global.TotalDeaths;
            this.recovered = data.Global.TotalRecovered;
          })
      },
      getDataByCountry() {
        const data = this.data.Countries.find(country => country.Slug == this.selectedCountry)
        this.confirmed = data.TotalConfirmed;
        this.deaths = data.TotalDeaths;
        this.recovered = data.TotalRecovered;
      }
    },
    mounted() {
      this.getCountries()
      this.getGlobalDatas()
    },
  }
</script>

<style>
  .globalInfo {
    margin-top: 50px;
    display: flex;
    justify-content: space-between;
  }
  .boxStyle {
    text-align: center;
    height: 100px;
    width: 300px;
    border: 1px solid black;
    display: grid;
    align-items: center;
    border-radius: 10px;
    background-color: whitesmoke;
    color: rgb(83, 83, 83);
  }
  .selectWrapper {
    display: flex;
    justify-content: center;
    height: 35px;
    margin-top: 50px;
  }
  .select {
    width: 100%;
    background-color: whitesmoke;
    border: 1px solid black;
    border-radius: 5px;
    text-align: center;
  }
  .mainPage {
    width: 1000px;
    margin: auto;
  }
  table {
    width: 100%;
    margin-top: 50px;
    background-color: whitesmoke;
    color: black;
    border: 1px solid black;
    border-radius: 3px;
    border-collapse: collapse;
    border-radius: 5px;
  }
  body {
    background-color: #293462;
    color: white;
  }
  .tableHeaders {
    height: 50px;
    border-bottom: 2px solid #293462;
  }
  .tableItems {
    height: 50px;
    border-bottom: 2px solid #293462;
  }
  .pageHeader {
    margin-top: 70px;
    margin-bottom: 70px;
    text-align: center;
  }
  .apiGlobalData {
    font-size: 35px;
  }
</style>