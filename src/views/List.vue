<template>
  <div class="container mt-2">
    <template v-if="isTasksEmpty">
      <div class="empty-data mt-2">
        <img src="../assets/empty-data.svg" class="empty-data-image">
        <b-button 
          variant="outline-primary" 
          class="mt-2" 
          size="lg"
          to="/form"
          >Criar tarefa </b-button>
      </div>
    </template>
    <template v-else>
      <div v-for="(task) in tasks" :key="task.id">
        <b-card :title="task.subject" class="mb-2">
          <b-card-text>{{ task.description }}</b-card-text>
          <b-button variant="outline-secondary" class="mr-2" @click="updateStatus(task.id, status.ARCHIVED)">Arquivar</b-button>
          <b-button variant="outline-secondary" class="mr-2" @click="edit(task.id)">Editar</b-button>
          <b-button variant="outline-danger" class="mr-2" @click="remove(task.id)">Excluir</b-button>
        </b-card>
      </div>
    </template>
    <b-modal ref="modalRemove" hide-footer title="Exclusão de Tarefa">
      <div class="d-block text-center">
        Deseja realmente excluir essa tarefa? {{ taskSelected.subject }}
      </div>
      <div class="mt-3 d-flex justify-content-end">
        <b-button variant="outline-secondary" class="mr-2" @click="hideModal">Cancelar</b-button>
        <b-button variant="outline-danger" class="mr-2" @click="confirmRemoveTask">Excluir</b-button>
      </div>
    </b-modal>
  </div>
</template>

<script>
import TasksModel from '@/models/TasksModel';
import Status from "@/valueObjects/status";
import ToastMixin from "@/mixins/toastMixin";

export default {
  name: "List",

  mixins: [ToastMixin],

  data() {
    return {
      tasks: [],
      taskSelected: [],
      status: Status
    }
  },

  async created() {
    this.tasks = await TasksModel.get();   
  },

  methods: {
    edit(taskId) {
      this.$router.push({ name: "form", params: { taskId }});
    },

    async remove(taskId) {
      this.taskSelected = await TasksModel.find(taskId);    
      this.$refs.modalRemove.show();
    },

    hideModal() {
      this.$refs.modalRemove.hide();
    },

    async confirmRemoveTask() {
      this.taskSelected.delete();
      this.tasks = await TasksModel.get();      
      this.hideModal();
    },

    async updateStatus(taskId, status) {
      let task = await TasksModel.find(taskId);
      task.status = status;
      await task.save();

      this.tasks = await TasksModel.params({ status: [this.status.OPEN, this.status.FINISHED]}).get();    
      this.showToast("success", "Sucesso!", "Status da Tarefa atualizado com sucesso");
    }
  },

  computed: {
    isTasksEmpty() {
      return this.tasks.length === 0;
    }
  }
}
</script>

<style scoped>
.empty-data{
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
}

.empty-data-image {
  width: 300px;
  height: 300px;
}
</style>
