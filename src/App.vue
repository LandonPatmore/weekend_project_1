<template>
  <div id="app">
    <h1>F1 Standings</h1>
    <h4>Select a year</h4>
    <select v-model='selectedYear' disabled>
      <option v-for='year in setOfYears' :key='year' :value='year'>{{year}} </option>
    </select>
    <input type="text" placeholder="Driver Information" v-model="driverSearch">
    <table>
      <thead>
        <th>Position</th>
        <th>Name</th>
        <th>Nationality</th>
        <th>Points</th>
        <th>Wins</th>
        <th>Team</th>
      </thead>
      <tbody>
        <tr v-for="driver in filteredDrivers" :key="driver.position">
          <td>{{driver.position}}</td>
          <td>{{driver.Driver.familyName}}</td>
          <td>{{driver.Driver.nationality}}</td>
          <td>{{driver.points}}</td>
          <td>{{driver.wins}}</td>
          <td>{{driver.Constructors[0].name}}</td>
        </tr>
      </tbody>
    </table>
    <table>
      <thead>
        <th>Position</th>
        <th>Name</th>
        <th>Points</th>
        <th>Wins</th>
        <th>Nationality</th>
      </thead>
      <tbody>
        <tr v-for="team in teamData" :key="team.position">
          <td>{{team.position}}</td>
          <td>{{team.Constructor.name}}</td>
          <td>{{team.points}}</td>
          <td>{{team.wins}}</td>
          <td>{{team.Constructor.nationality}}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'app',
  data: function() {
    return {
      setOfYears: [],
      currentYear: new Date().getFullYear(),

      driversData: [],
      teamData: [],

      selectedYear: '',

      driverSearch: ''
    }
  },

  methods: {
    setYears: function() {
      let firstYear = 1950
      let loopedYear = this.currentYear
      let setOfYears = []

      while (loopedYear >= firstYear) {
        setOfYears.push(loopedYear)
        loopedYear--
      }

      return setOfYears
    },

    grabDriverStandings: function(year) {
      return axios.get('../static/driverData.json')
    },

    grabConstructorStandings: function(year) {
      return axios.get(`../static/teamData.json`)
    },

    grabAllData: function(year) {
      var that = this

      axios.all([this.grabDriverStandings(year), this.grabConstructorStandings(year)])
        .then(axios.spread(function(drivers, constructors) {
          that.driversData = drivers.data.MRData.StandingsTable.StandingsLists[0].DriverStandings
          that.teamData = constructors.data.MRData.StandingsTable.StandingsLists[0].ConstructorStandings
        }))
    }
  },

  watch: {
    selectedYear: function() {
      this.grabAllData(this.selectedYear)
    }
  },

  created: function() {
    this.setOfYears = this.setYears()
    this.grabAllData(this.currentYear)
  },

  computed: {
    filteredDrivers: function() {
      return this.driversData.filter((driver) => {
        return driver.Driver.familyName.match(new RegExp(this.driverSearch, 'i')) || driver.Driver.nationality.match(new RegExp(this.driverSearch, 'i')) || driver.points.match(new RegExp(this.driverSearch, 'i')) || driver.Constructors[0].name.match(new RegExp(this.driverSearch, 'i'))
      })
    }
  }
}
</script>

<style lang='scss'>
@import url('https://fonts.googleapis.com/css?family=Roboto');

$font: Roboto,
sans-serif;
$transparency: rgba(255,
255,
255,
0.3);

#app {
  font-family: $font;
  height: 100%;
  width: 100%;

  h1 {
    margin: 0;
    font-size: 3.0rem;
  }

  select {
    font-size: 1.5rem;
  }

  table {
    text-align: left;
    border-collapse: collapse;
    table-layout: fixed;
    text-transform: uppercase;
    width: 45%;
    margin: auto;

    thead {
      font-size: 1.2rem;
    }

    td,
    th {
      padding: 0.5rem;
      min-width: 100px;
    }

    tr {
      border: 1px solid $transparency;
    }

    tr:nth-child(odd) {
      background: #EEE;
    }

    tr:hover {
      box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.19);
    }

    thead {
      background: $transparency;
    }
  }
}
</style>
