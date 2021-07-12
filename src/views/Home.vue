<template>
  <div>
    <div v-show="showAddTask">
      <AddTask @add-task="addTask" />
    </div>
    <!-- v-bind ":tasks" because we expect the data to be dynamic, which the data will
  pass to the Tasks.vue component -->
    <Tasks
      @toggle-reminder="toggleReminder"
      @delete-task="deleteTask"
      :tasks="tasks"
    />
  </div>
</template>

<script>
import Tasks from "../components/Tasks";
import AddTask from "../components/AddTask";

export default {
  name: "Home",
  props: {
      showAddTask: Boolean
  },
  components: {
    Tasks,
    AddTask,
  },
  data() {
    return {
      tasks: [],
    };
  },
  methods: {
    async addTask(task) {
      const res = await fetch("api/tasks", {
        method: "POST",
        // headers is additional information pass between client and server, it improves client experience by specifying
        // what is the type of data being transferred between the client and server.
        headers: {
          "Content-type": "application/json",
        },
        body: JSON.stringify(task),
      });

      const data = await res.json();
      // spread across the tasks data and add a new task to the end
      this.tasks = [...this.tasks, data];
    },
    async deleteTask(id) {
      if (confirm("Are you sure?")) {
        // console.log('task', id)
        const res = await fetch(`api/tasks/${id}`, {
          method: "DELETE",
        });

        res.status === 200
          ? (this.tasks = this.tasks.filter((task) => task.id !== id))
          : alert("Error Deleting Task");
        // this.tasks = this.tasks.filter((task) => task.id !== id);
      }
    },

    async toggleReminder(id) {
      // NOTE: be careful with the constance reference of fetchTask and fetchTasks - both a different thing!!!
      const taskToToggle = await this.fetchTask(id);
      const updTask = { ...taskToToggle, reminder: !taskToToggle.reminder };

      const res = await fetch(`api/tasks/${id}`, {
        method: "PUT", // put is for update
        headers: {
          "Content-type": "application/json",
        },
        body: JSON.stringify(updTask),
      });

      const data = await res.json();
      // console.log(id)
      // changing the reminder to either true to false, or false to true
      // .map maps through the entire array and let us manipulate the task, then return an updated task.
      this.tasks = this.tasks.map(
        (task) => (task.id === id ? { ...task, reminder: data.reminder } : task)
        // ...task the initial task property
        // change the "remainder" element to whatever the opposite of current reminder
        // else, return the initial task
      );
    },

    async fetchTasks() {
      const res = await fetch("api/tasks");

      const data = await res.json();

      return data;
    },

    async fetchTask(id) {
      // in production, use api/tasks
      const res = await fetch(`api/tasks/${id}`);

      const data = await res.json();

      return data;
    },
  },
  async created() {
    this.tasks = await this.fetchTasks();
    // tasks is defined in data()
    // this.tasks = [
    //   {
    //     id: "1",
    //     text: "Doctors Appointment",
    //     day: "March 5th at 2:30pm",
    //     reminder: true,
    //   },
    //   {
    //     id: "2",
    //     text: "Meeting with boss",
    //     day: "March 6th at 1:30pm",
    //     reminder: true,
    //   },
    //   {
    //     id: "3",
    //     text: "Food shopping",
    //     day: "March 7th at 2:00pm",
    //     reminder: false,
    //   },
    // ];
  },
};
</script>

<style>
</style>