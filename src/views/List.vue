<template>
  <div class="container mt-2">
    <div v-for="(task, index) in tasks" :key="index">
      <b-card :title="task.subject" class="mb-2">
        <b-card-text>{{ task.description }}</b-card-text>
        <b-button variant="outline-secondary" class="mr-2" @click="edit(index)">Editar</b-button>
        <b-button variant="outline-danger" class="mr-2">Excluir</b-button>
      </b-card>
    </div>
  </div>
</template>

<script>
export default {
  name: "List",

  data() {
    return {
      tasks: []
    }
  },

  created() {
    this.tasks = (localStorage.getItem("tasks")) ? JSON.parse(localStorage.getItem("tasks")) : [];
  },

  methods: {
    edit(index) {
      this.$router.push({ name: "form", params: { index }});
    }
  }
}
</script>
