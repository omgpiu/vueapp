<template>
  <div class='container'>
    <Header
        :isFormShow='isFormShow'
        @show-form='toggleTaskForm' />
    <div v-show='isFormShow'>
      <TaskForm @add-task='addTask' />
    </div>

    <Tasks
        @toggle-reminder='changeReminder'
        @delete-task="deleteTask" :tasks='tasks' />
    <router-view></router-view>
    <Footer />
  </div>
</template>

<script>


import Header from './components/Header';
import Tasks from './components/Tasks';
import TaskForm from './components/TaskForm';
import Footer from './components/Footer';


export default {
  name: 'App',
  components: {
    Header,
    Tasks,
    TaskForm,
    Footer

  },
  data() {
    return {
      tasks: [],
      isFormShow: false,
    }
  },
  methods: {
    async deleteTask(id) {
      if (confirm('Are you sure?')) {
        try {
          const res = await fetch(`api/tasks/${id}`, {
            method: 'DELETE',
          })
          res.status === 200 ? this.tasks = this.tasks.filter(t => t.id !== id) : null
        } catch (e) {
          console.log('some error')
        }

      }
    },
    async changeReminder(id) {
      const toggledTask = await this.fetchTask(id)
      const updTask = { ...toggledTask, reminder: !toggledTask.reminder }
      const res = await (await fetch(`api/tasks/${id}`, {
        method: 'PUT',
        headers: {
          'Content-type': 'application/json',
        }, body: JSON.stringify(updTask)
      })).json()

      this.tasks = this.tasks.map(t => t.id === id ? { ...t, reminder: res.reminder } : t)
    },
    async addTask(task) {
      const res = await fetch(`api/tasks`, {
        method: 'POST',
        headers: {
          'Content-type': 'application/json',
        }, body: JSON.stringify(task)
      })
      const data = await res.json()
      this.tasks = [data, ...this.tasks]
    },
    toggleTaskForm() {
      this.isFormShow = !this.isFormShow
    },
    async fetchTasks() {
      return (await fetch(`api/tasks`)).json()
    },
    async fetchTask(id) {
      return (await fetch(`api/tasks/${id}`)).json()
    },

  },
  async created() {
    this.tasks = await this.fetchTasks()


  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@300;400&display=swap');

* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  font-family: 'Poppins', sans-serif;
}

.container {
  max-width: 500px;
  margin: 30px auto;
  overflow: auto;
  min-height: 300px;
  border: 1px solid steelblue;
  padding: 30px;
  border-radius: 5px;
}

.btn {
  display: inline-block;
  background: #000;
  color: #fff;
  border: none;
  padding: 10px 20px;
  margin: 5px;
  border-radius: 5px;
  cursor: pointer;
  text-decoration: none;
  font-size: 15px;
  font-family: inherit;
}

.btn:focus {
  outline: none;
}

.btn:active {
  transform: scale(0.98);
}

.btn-block {
  display: block;
  width: 100%;
}
</style>
