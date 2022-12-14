<template>
  <div class="container">
    <select v-model="sortingOption">
      <option value="">Without filter</option>
      <option v-for="(option, ind) in columns" :key="ind">{{option}}</option>
    </select>
    <input v-model="filterOptionValue"/>
    <button @click="filterData">Find</button>
    <button @click="showModal = true">Add User</button>
    <div v-if="isLoading" class="spinner">
      <VueSpinnerIos color="black" size="100px"/>
    </div>
    <table v-else>
      <thead>
        <tr>
          <th v-for="(column, index) in columns" :key="index" @click="getSortValue">{{column}}</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="user in sortedAndFilteredTable" :key="user.email">
          <td v-for="value in columns" :key="value.email">{{user[value]}}</td>
        </tr>
      </tbody>
    </table>
    <div ref='observer' class="observer"></div>
  </div>
  <ModalWindow v-model:showModal="showModal">
    <form class="user-form" @submit.prevent>
      <input v-model="newUser.firstName" placeholder="first name"/>
      <input v-model="newUser.lastName" placeholder="last name"/>
      <input v-model="newUser.email" placeholder="email" type="email"/>
      <input v-model="newUser.phone" placeholder="phone"/>
      <input v-model="newUser.address" placeholder="address"/>
      <input v-model="newUser.description" placeholder="description"/>
      <button @click="addNewUser">Add user</button>
    </form>
  </ModalWindow>
</template>

<script>
import { VueSpinnerIos } from 'vue3-spinners'
import ModalWindow from './components/ModalWindow.vue'

export default {
  components: {
    VueSpinnerIos, ModalWindow
  },
  name: 'App',
  data () {
    return {
      users: [],
      columns: [],
      sortingValue: '',
      sortingOption: '',
      sortingReverse: false,
      filterOption: '',
      filterOptionValue: '',
      isLoading: false,
      showModal: false,
      newUser: {
        id: 1,
        firstName: '',
        lastName: '',
        email: '',
        phone: '',
        address: '',
        description: ''
      }
    }
  },
  methods: {
    async getUsersData () {
      this.isLoading = true
      const result = await fetch('http://www.filltext.com/?rows=15&id={number|1000}&firstName={firstName}&lastName={lastName}&email={email}&phone={phone|(xxx)xxx-xx-xx}&address={addressObject}&description={lorem|32}')
      if (!result.ok) {
        throw new Error('not fetch')
      }
      const body = await result.json()
      const data = await body
      this.users = data
      this.setColumns()
      this.isLoading = false
    },
    async getMoreUserData () {
      const result = await fetch('http://www.filltext.com/?rows=15&id={number|1000}&firstName={firstName}&lastName={lastName}&email={email}&phone={phone|(xxx)xxx-xx-xx}&address={addressObject}&description={lorem|32}')
      if (!result.ok) {
        throw new Error('not fetch')
      }
      const body = await result.json()
      const data = await body
      this.users = [...this.users, ...data]
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
    },
    filterData () {
      this.filterOption = this.filterOptionValue
    },
    addNewUser () {
      const newUser = { ...this.newUser }
      this.users.unshift(newUser)
      this.showModal = false
    }
  },
  mounted () {
    this.getUsersData()
    const options = {
      rootMargin: '0px',
      threshold: 1.0
    }
    const callback = (entries, observer) => {
      console.log(observer)
      console.log(entries[0].isIntersecting)
      if (entries[0].isIntersecting) {
        this.getMoreUserData()
      }
    }
    const observer = new IntersectionObserver(callback, options)
    observer.observe(this.$refs.observer)
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
    },
    sortedAndFilteredTable () {
      if (this.sortingOption && this.filterOption) {
        console.log('this.filterOption', this.filterOption)
        return this.sortedTable.filter(item => item[this.sortingOption].toString().toLowerCase().includes(this.filterOption))
      } else {
        return this.sortedTable
      }
    }
  },
  watch: {
    sortingOption () {
      this.filterOptionValue = ''
      this.filterData()
    }
  }
}
</script>

<style scoped>
.container {
  width: 1280px;
  margin: 0 auto;
}

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

tr {
  position: relative;
}

.page-container {
  display: flex;
  justify-content: center;
  gap: 15px;
  margin-top: 15px;
}

.page {
  cursor: pointer;
}

.spinner {
  display: flex;
  justify-content: center;
  padding: 30px;
}

.active {
  transform: scale(1.1);
  font-weight: bold;
}

.close {
  position: absolute;
  top: 0;
  right: -30px;
  cursor: pointer;
  color: black;
}

.user-form {
  border: 1px solid green;
  border-radius: 5px;
  display: flex;
  flex-direction: column;
  gap: 10px;
  padding: 15px;
}

</style>
