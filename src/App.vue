<template>
  <table>
    <thead>
      <tr>
        <th v-for="(column, index) in columns" :key="index" @click="getSortValue">{{column}}</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="user in sortedTable" :key="user.id">
        <td v-for="value in columns" :key="value.email">{{user[value]}}</td>
      </tr>
    </tbody>
  </table>
</template>

<script>

export default {
  name: 'App',
  data () {
    return {
      users: [],
      columns: [],
      sortingValue: '',
      sortingReverse: false
    }
  },
  methods: {
    async getUsersData () {
      const result = await fetch('http://www.filltext.com/?rows=32&id={number|1000}&firstName={firstName}&lastName={lastName}&email={email}&phone={phone|(xxx)xxx-xx-xx}&address={addressObject}&description={lorem|32}')
      if (!result.ok) {
        throw new Error('not fetch')
      }
      const body = await result.json()
      this.users = await body
      this.setColumns()
    },
    setColumns () {
      for (let index = 0; index < this.users.length; index++) {
        for (const key in this.users[index]) {
          if (!this.columns.includes(key)) {
            this.columns.push(key)
          }
        }
      }
    },
    getSortValue (e) {
      if (this.sortingValue === e.target.textContent) this.sortingReverse = !this.sortingReverse
      this.sortingValue = e.target.textContent
      console.log(this.sortingReverse)
    }
  },
  mounted () {
    this.getUsersData()
  },
  computed: {
    sortedTable () {
      if (this.sortingValue && !this.sortingReverse) {
        return [...this.users].sort((a, b) => a[this.sortingValue] > b[this.sortingValue] ? 1 : -1)
      } else if (this.sortingValue && this.sortingReverse) {
        return [...this.users].sort((a, b) => b[this.sortingValue] > a[this.sortingValue] ? 1 : -1)
      } else {
        return this.users
      }
    }
  }
}
</script>

<style>
table,
td {
    border: 1px solid #333;
    padding: 5px;
}

thead,
tfoot {
    background-color: #333;
    color: #fff;
}

th,
td {
  padding: 10px;
}

th {
  cursor: pointer;
}

</style>
