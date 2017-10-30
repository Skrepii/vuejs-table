<template>
  <div id="app">
    <img src="./assets/logo.png">
    <h1>Static tables are no fun</h1>
    <h2>You should be able to <b>sort</b> by clicking a column-header and <b>filter</b> by entering a search term below</h2>
    <h3>... also dates and numbers should be formatted better</h3>
    <div class="filter">
      <label for="filter">Filter</label>
      <input id="filter" placeholder="Enter search term..."></input>
    </div>
    <table>
      <thead>
        <tr>
          <th>Name</th>
          <th>Age</th>
          <th>CPR</th>
          <th>Diagnosis</th>
          <th>Admitted date</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="pt in patients">
          <td>{{ pt.name }}</td>
          <td>{{ pt.age }}</td>
          <td>{{ pt.cpr }}</td>
          <td>{{ pt.diagnosis }}</td>
          <td>{{ pt.admitted }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  name: 'app',
  data () {
    return {
      patients: [
        { name: 'John Doe', age: 42, cpr: '123456-0001', diagnosis: 'Broken leg', admitted: new Date().setHours(8) },
        { name: 'Jane Doe', age: 21, cpr: '123456-0002', diagnosis: 'Broken arm', admitted: new Date().setHours(9) },
        { name: 'Ben Johnson', age: 80, cpr: '123456-0003', diagnosis: 'Concussion', admitted: new Date().setHours(10) },
      ]
    }
  }
}
</script>

<style lang="scss">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.filter {
  padding: 10px;
  width: 800px;
  margin: auto;
  margin-top: 50px;
  margin-bottom: 50px;
  border: 1px solid green;
}

table {
  width: 800px;
  margin: auto;
}

h1, h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}
</style>
