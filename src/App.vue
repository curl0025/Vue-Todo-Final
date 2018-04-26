<template>
  <div id="app">

    <div v-if="isLoggedIn">
       <h1 class="title">Vue Todos</h1>
       <div class="header">
         <button class="out" @click="logout">LOGOUT</button>
         <nav class="toggle">
      <ul>
      
        <div class="all">
        <li><router-link to="/">All</router-link></li>
        </div>
        <div class="active">
        <li><router-link to="/active">Active</router-link></li>
        </div>
        <div class="completed">
        <li><router-link to="/completed">Completed</router-link></li>
        </div>
        
      </ul>
    </nav>

       
           <add-task-form @taskAdded="addTask" />
</div>
       
 

    <router-view
      :tasks="taskList"
      @toggleDone="toggleDone"
      @removeTask="removeTask"
    />
  </div>
  <login-page v-else @saveApiTokens="saveApiTokens" />
  </div>
</template>
      


<script>
/* eslint-disable */
import axios from 'axios'
import moment from 'moment'
import AddTaskForm from '@/components/AddTaskForm'
import LoginPage from '@/pages/LoginPage'
 
export default {
  name: 'App',
  components: { LoginPage, AddTaskForm },
  data () {
    return {
      baseURL: 'https://vue-todos.robertmckenney.ca/api',
      api: {
        accessToken: null,
        expiresAt: null
      },
      taskList: [
        // { id: 1234, title: 'Learn Vue', isComplete: true, priority: 'medium' },
        // { id: 1235, title: 'Learn Vue Router', isComplete: false, priority: 'medium' },
        // { id: 1236, title: 'Learn Vuex', isComplete: false, priority: 'medium' },
        // { id: 1237, title: 'Learn Vue DevTools', isComplete: true, priority: 'medium' }
      ]
    }
  },

  created () {
   // this.taskList = JSON.parse(localStorage.getItem('taskList')) || []
     
  axios
  .get(`${this.baseURL}/priorities`)
  .then(response => {
    this.priorityOptions = response.data.data.length
     ? response.data.data
     : []
  })

  .catch(error => { console.error(error)})
},
  

  methods: {
    refreshTasks () {
    axios
      .get('/todos', this.axiosOptions)
      .then(response => { this.taskList = response.data.data })
      .catch(error => { this.handleError(error) })
  },
  addTask (task) {
    axios
      .post('/todos', task, this.axiosOptions)
      .then(response => { this.taskList.push(response.data.data) })
      .catch(error => { this.handleError(error) })
  },
  toggleDone (task) {
    task.isComplete = !task.isComplete
    axios
      .put(`/todos/${task.id}`, task, this.axiosOptions)
      .catch(error => { this.handleError(error) })
  },
  removeTask (task) {
    axios
      .delete(`/todos/${task.id}`, this.axiosOptions)
      .then(response => {
        const taskIndex = this.taskList.findIndex(t => t.id === task.id)
        this.taskList.splice(taskIndex, 1)
      })
      .catch(error => { this.handleError(error) })
  },
  handleError(error) {
    console.log(error)
    // obviously we want to do something more robust
    // including notifying the user somehow.
  },
    saveTaskList () {
      localStorage.setItem('taskList', JSON.stringify(this.taskList))
    
  },

  saveApiTokens (apiTokens) {
  this.api.accessToken = apiTokens.access_token
  this.api.expiresAt = apiTokens.expires_at
  localStorage.setItem('todoApiTokens', JSON.stringify(apiTokens))
  this.refreshTasks()
},

logout () {
  this.api.accessToken = null
  this.api.expiresAt = null
  localStorage.removeItem('todoApiTokens')
}

  },
computed: {
    isLoggedIn () {
      return this.api.accessToken && moment(this.api.expiresAt).isAfter()
    },

    axiosOptions () {
      return {
        baseURL: this.baseURL,
        headers: { 'Authorization' : `Bearer ${this.api.accessToken}`}

      }
    }
}
  }




</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  /* text-align: center; */
  color: #2c3e50;
  width: 100%;
  max-width: 550px;
  margin: 60px auto;
}
ul {
  list-style-type: none;
  margin: 0;
  padding: 0;
  
  
}
nav ul {
  display: flex;
  justify-content: flex-start;
}
nav ul li {
  
  text-align: center;
  margin: 0 auto;
  margin-bottom: 50px;
  
  
}

.header {
  
  width: 100%;
}



.title {
  background: #42b883;
  text-align: center;
}


.all {
  
  border: 2px solid black;
  border-radius: 20px;
  height: 20px;
  width: 90px;
  background: #42b883;
}

.active {
  
  border: 2px solid black;
  border-radius: 20px;
  height: 20px;
  width: 90px;
  background: #42b883;
  cursor: pointer;
}

.completed {
  
  border: 2px solid black;
  border-radius: 20px;
  height: 20px;
  width: 90px;
  background: #42b883;
  cursor: pointer;
}

.toggle {
  width: 300px;
  height: 40px;
  margin: 0 auto;
  margin-bottom: 50px;
  cursor: pointer;
  
  
}

.form {
  text-align: center;
}

input#email {
  width: 250px;
}

 input#password {
   width: 250px;
 }

 .form-group {
   margin-bottom: 50px;
 }

.out {
  float: right;
}


</style>
