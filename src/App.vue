<template>
  <div id="app">
    <img src="./assets/logo.png">
    <h1>Static tables are no fun</h1>
    <h2>You should be able to <b>sort</b> by clicking a column-header and <b>filter</b> by entering a search term below</h2>
    <h3>... also dates and numbers should be formatted better</h3>
    <div class="filter">
      <label for="filter">Filter</label>
      <input v-model="filterQuery" id="filter" placeholder="Enter search term..."></input>
    </div>
    <table>
      <thead>
        <tr v-on:click="sort" class="clickable">
          <th>Name</th>
          <th>Age</th>
          <th>CPR</th>
          <th>Diagnosis</th>
          <th v-on:click.stop="sort($event, 'admitted')">Admitted date</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="pt in filteredPatients">
          <td>{{ pt.name }}</td>
          <td>{{ pt.age | age }}</td>
          <td>{{ pt.cpr | cpr(pt.age)}}</td>
          <td>{{ pt.diagnosis }}</td>
          <td>{{ pt.admitted | dateTime}}</td>
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
        { name: 'John Doe', age: "1975-07-10", cpr: '0001', diagnosis: 'Broken leg', admitted: new Date().setHours(8) },
        { name: 'Jane Doe', age: "1996-11-21", cpr: '0002', diagnosis: 'Broken arm', admitted: new Date().setHours(9) },
        { name: 'Ben Johnson', age: "1937-01-05", cpr: '0003', diagnosis: 'Concussion', admitted: new Date().setHours(10) },
      ],
      sorting: {
        selectedHeader: null,
        asc: true
      },
      filterQuery: ''
    }
  },
  computed: {
    filteredPatients: function () {
      return this.patients.filter(item => {
        // Get all the entries ([key, value] pairs) from object.
        let itemValues = Object.entries(item)
        // Looking if any value returns true.
        return itemValues.some(property  => {
          let key = property[0]
          let value = property[1]
          switch (key) {
            case "admitted":
              return this.$options.filters.dateTime(value).includes(this.filterQuery)
            case "age":
              return this.$options.filters.age(value).includes(this.filterQuery)
            case "cpr":
              let birthDate = itemValues.find(item => item[0] === "age")[1]
              return this.$options.filters.cpr(value, birthDate).includes(this.filterQuery)
            default:
              return value.toString()
                .toLowerCase()
                .includes(this.filterQuery.toLowerCase())
          }
        })
      })
    }
  },
  filters: {
    dateTime: function (value) {
      if (!value) return ''

      // Format value to Date object for easier data manipulation.
      let date = new Date(value) 
      // Adding zeroes in front for readability.
      let hour = addZero(date.getUTCHours())
      let minutes =  addZero(date.getUTCMinutes())
      let day = addZero(date.getUTCDate())
      let month = addZero(date.getUTCMonth() + 1) // + 1 as Months are counted from 0-11.

      return `${day}-${month}-${date.getUTCFullYear()} ${hour}:${minutes}`
    },
    age: function (value) {
      if (!value) return ''

      let now = new Date()
      let birthDate = new Date(value)
      let age = now.getUTCFullYear() - birthDate.getUTCFullYear()

      if (now.getUTCMonth() < birthDate.getUTCMonth()) {
        age--
      }else if (now.getUTCMonth() === birthDate.getUTCMonth()) {
        // For setting up the edge cases of age
        if(now.getUTCDate() <= birthDate.getUTCDate()) { 
          age--
        }
      }

      return `${value} (${age})`
    },
    cpr: function (value, birthDate) {
      if (!value) return ''

      let date = new Date(birthDate)
      let day = addZero(date.getUTCDate())
      let month = addZero(date.getUTCMonth() + 1) // + 1 as Months are counted from 0-11.
      let year = addZero(date.getUTCFullYear())

      return `${day}${month}${year}-${value}`
    }
  },
  methods: {
    sort: function (event, field) {
      // Remove a span element if one exists.
      let span = document.getElementById("sort-icon")
      if(span) {
        span.remove()
      }

      // Check if the user has clicked on a SPAN or TH element to route accordingly.
      let selectedHeader = (event.target.nodeName !== "TH") ? event.path[1] : event.target
      // Declare a key with which we can access "dynamically" certain columns.
      let key = field || selectedHeader.firstChild.nodeValue.toLowerCase()
      // If we have selected a different column we start with ordering in ascending order.
      if(selectedHeader != this.sorting.selectedHeader) {
        this.sorting.asc = true
      }

      span = document.createElement("span") // Creating span element for asc, desc indication on column.
      span.textContent = (this.sorting.asc) ? " (asc)" : " (desc)"
      span.id = "sort-icon"
      selectedHeader.append(span)

      this.patients.sort((a, b) => {
        // Transform any string to lowercases so the sorting would not be case sensitive.
        a = (typeof a[key] === 'string') ? a[key].toLowerCase() : a[key]
        b = (typeof b[key] === 'string') ? b[key].toLowerCase() : b[key]

        let asc = 0
        if (a > b) {
          asc = 1
        } else if (a < b) {
          asc = -1
        }
        // If the this.sorting.asc is false then sort the list desc (in reverse).
        return (this.sorting.asc) ? asc : -asc
      })

      this.sorting.asc = !this.sorting.asc
      this.sorting.selectedHeader = selectedHeader // Indicate which column we have ordered by.
    }
  }
}
// Helper methods.
function addZero(number) {
  return ('0' + number).slice(-2)
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

.clickable > th {
  cursor: pointer;
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
