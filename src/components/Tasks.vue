<template>
  <!-- v-for loop through something, a list. -->
  <!-- When you create a list, you have to have a unique key -->
  <div>
    <div :key="task.id" v-for="task in tasks">
      <Task @toggle-reminder="$emit('toggle-reminder', task.id)" @delete-task="$emit('delete-task', task.id)" :task="task" />
    </div>
  </div>
</template>

<script>
import Task from './Task'

export default {
  name: "Tasks",
  props: {
    tasks: Array,
  },
  components: {
      Task
  },
  // define emits = just an array of the thing we are defining, ie. delete-task from app.vue
  emits: ['delete-task', 'toggle-reminder']
};
</script>
