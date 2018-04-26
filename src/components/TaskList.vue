<template>
  <div>
    <h2>{{listTitle}}</h2>
    <div class="toolbar">
      <label for="priority-filter">Show priority</label>
      <select name="priority-filter" id="priority-filter" v-model="selectedPriority">
        <option value="">all</option>
        <option
          v-for="option in priorityOptions"
          :value="option"
          :key="option">{{option}}</option>
      </select>
      <label for="category-filter">Show category</label>
       <button @click="sortAscending = !sortAscending">Sort</button>
      <select name="category-filter" id="category-filter" v-model="selectedCategory">
        <option value="">all</option>
        <option
          v-for="option in categoryOptions"
          :value="option"
          :key="option">{{option}}</option>
      </select>
    </div>
    <ul class="task-list">
      <li v-for="task in sortedTasks" :key="task.id" class="task-item">
        <span class="far"
          :class="{
            'fa-circle': ! task.isComplete,
            'fa-check-circle': task.isComplete
          }"
          @click="toggleDone(task)"></span>
        <div class="item-title">
        <span>{{ task.title }}</span>
        </div>

        <div class="desc">
        <span>{{ task.description }}</span>
        </div>

        <div class="category">
        <span>{{ task.category }}</span>
        </div>
         <div class="priority">
        <span>{{ task.priority.name }}</span>
        </div>

        <div class="date">
        <span>{{ task.dueAt }}</span>
        </div>
          
        <span class="far fa-trash-alt" @click="removeTask(task)"></span>
      </li>
    </ul>
  </div>
</template>

<script>
import axios from 'axios'
import moment from 'moment'

export default {
  name: 'task-list',
  props: ['tasks', 'listTitle'],
  data () {
    return {
     
      priorityOptions: ['Low', 'Medium', 'High'],
      categoryOptions: ['Home', 'School', 'Work'],
      selectedPriority: '',
      selectedCategory: '',
      sortAscending: true,
      
    }
  },




  computed: {
    filteredPriorityTasks () {
      // If the selectedFilter is 'all', the value is set to an empty string
      // which will evaluate to false. In that case, we will reuturn the
      // entire task list. Otherwise we use the array.filter() method to return
      // only the tasks with the selected priority.
      return (!this.selectedPriority)
        ? this.tasks
        : this.tasks.filter(task => task.priority === this.selectedPriority)
    },
    filteredCategoryTasks () {
      return (!this.selectedCategory)
        ? this.tasks
        : this.tasks.filter(task => task.category === this.selectedCategory)
    },
    filteredTasks () {
      const combined = [
        ...this.filteredPriorityTasks,
        ...this.filteredCategoryTasks
      ]
      const merged = [...new Set(combined)]
      return merged.filter(task =>
        this.filteredPriorityTasks.includes(task) &&
        this.filteredCategoryTasks.includes(task)
      )
    },
   sortedTasks () {
      return [...this.filteredTasks].sort((a, b) =>{
        const dateOne = new Date(a.dueAt)
        const dateTwo = new Date(b.dueAt)
        return this.sortAscending ? (dateOne - dateTwo) : (dateTwo - dateOne)
      })
    },
    
  },
  methods: {
    // Since this component does not own the task list data, we need to
    // notify the parent component that the user wants to toggle the
    // completion status of the task.  We do that by emitting a custom event,
    // and passing up the affected task with the event. We will listen for
    // this event in the parent component and call the appropriate method to
    // make the change to the data.
    toggleDone (task) {
      this.$emit('toggleDone', task)
    },
    removeTask (task) {
      this.$emit('removeTask', task)
    }
  }
}
</script>
<style>

/* We will add styles here later. */
.task-item {
 max-width: 400px;
 height: 200px;
 border: 2px solid #42b883;
 border-radius: 5px;
 margin: 0 auto;
 margin-top: 50px;
}
.toolbar {
  margin-bottom: 1rem;
}

h1 {
  text-align: center;
}

h2 {
  text-align: center;
  color: #35495e;
}

section {
  text-align: center;
}

.priority {
  text-align: center;
}

select {
  border: 2px solid #35495e;
  cursor: pointer;
}

.task-title {
  border: 2px solid #35495e;
}

.item-title {
  text-align: center;
  font-size: 20px;
  font-weight: bold;
  padding: 8px;
}

.desc {
  padding: 5px;
  font-size: 15px;
}

.date {
  margin-top: 20px;
  color: blue;
}

.category {
  padding: 5px;
}

.fa-circle, .fa-check-circle {
  float: left;
}

.fa-trash-alt {
  float: left;
}
</style>
