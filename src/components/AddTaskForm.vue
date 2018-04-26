<template>
  <div class="box">
    
    <div class="date-label">
    <label>Due date
      
      <input type="date" v-model="newTask.dueDate">
    
    </label>
    </div>
    <label>Title
      
      <input class="task-title" type="text" v-model.trim="newTask.title" @keyup.enter="addTask">
      
    </label>

     <label>Description
      <input class="task-description" type="text" v-model.trim="newTask.description" @keyup.enter="addTask">
      
    </label>
  
    <select v-model="newTask.priority">
      <option value="1">High</option>
      <option value="2">Medium</option>
      <option value="3">Low</option>
    </select>
    
    <select v-model="newTask.category">
      <option value="home">Home</option>
      <option value="school">School</option>
      <option value="work">Work</option>
    </select>

    
     <div class="new-button">
     <i class="fas fa-plus" @click="addTask(newTask)"></i>
     </div>

  
  </div>
</template>

<script>
export default {
  data () {
    return {
      newTask: {}
    }
  },

  created () {
    this.newTask = this.getNewTask()
  },

  methods: {
    addTask () {
      // this.taskList.push(this.newTask)
      this.$emit('taskAdded', this.newTask)
      this.newTask = this.getNewTask()
    },

    getNewTask () {
      const now = new Date()
      const today = now.toISOString().substr(0, now.toISOString().indexOf('T'))
      console.log(now)
      return {
        id: null,
        category: '',
        dueDate: today,
        isComplete: false,
        priority: '2',
        title: '',
        description: '',
      }
    }
  },


}
</script>

<style>
.fa-plus {
  font-size: 30px;
  margin-top: 30px;
  cursor: pointer;
}

.new-button {
  text-align: center;
}

.date-label {
  text-align: center;
  margin-bottom: 50px;
}

.box {
 margin: 0 auto;
 text-align: center;
}
</style>
