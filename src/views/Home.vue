<template>
  <TaskForm v-show='isFormShow' @add-task='addTask' />
  <Tasks
      @toggle-reminder='changeReminder'
      @delete-task="deleteTask"
      :tasks='tasks' />


</template>

<script>


import Tasks from '../components/Tasks';
import TaskForm from '../components/TaskForm';

export default {
  name: 'Home',
  components: {
    Tasks,
    TaskForm,
  },
  props: {
    isFormShow: Boolean
  },
  data() {
    return {
      tasks: [],
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
