<template>
  <div id="app">
    <h1>F1 Standings</h1>
    <h4>Select a year</h4>
    <select v-model='selectedYear'>
      <option v-for='year in setOfYears' :key='year' :value='year'>{{year}}</option>
    </select>
    <h2>Driver Standings</h2>
    <table>
      <thead>
        <th>Position</th>
        <th>Name</th>
        <th>Number</th>
        <th>Points</th>
        <th>Wins</th>
        <th>Team</th>
      </thead>
      <tbody>
        <tr v-for="driver in driversData" :key="driver.position" v-if='driver.Driver.permanentNumber'>
          <td>{{driver.position}}</td>
          <td>{{driver.Driver.familyName}}</td>
          <td>{{driver.Driver.permanentNumber}}</td>
          <td>{{driver.points}}</td>
          <td>{{driver.wins}}</td>
          <td>{{driver.Constructors[0].name}}</td>
        </tr>
      </tbody>
    </table>
    <hr>
    <h2>Constructor Standings</h2>
    <router-view></router-view>
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

      selectedYear: ''
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

    tester: function() {
      console.log('tester method')
    },

    grabDriverStandings: function(year) {
      axios.get(`http://ergast.com/api/f1/` + year + `/driverStandings.json`)
        .then(response => {
          // JSON responses are automatically parsed.
          this.driversData = response.data.MRData.StandingsTable.StandingsLists[0].DriverStandings
        })
        .catch(e => {
          this.errors.push(e)
        })
    }
  },

  watch: {
    selectedYear: function() {
      this.grabDriverStandings(this.selectedYear)
    }
  },

  created: function() {
    this.setOfYears = this.setYears()
    this.grabDriverStandings(this.currentYear)
  }
}
</script>

<style lang='scss'>
#app {}

select {
  font-size: 1.5rem;
}

table {
  text-align: center;
  border-collapse: collapse;

  thead {
    font-size: 1.2rem;
  }

  td,
  th {
    border: 1px solid black;
    padding: 0.2rem;
    min-width: 100px;
  }

  tr:hover{
    background: red;
  }

  thead{
    box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.19);
  }
}
</style>
