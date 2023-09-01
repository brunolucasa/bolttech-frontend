<template>
  <div>
    <input
      type="text"
      v-model="titleTask"
      style="background: red"
      placeholder="Escreva aqui sua task"
    />
    <input
      type="number"
      v-model="idProjeto"
      style="background: red"
      placeholder="teu ID"
    />
    <button @click="adicionarTarefa">adicionar tarefa</button>
  </div>
</template>

<!-- Script Requests -->
<script>
export default {
  data() {
    return {
      titleTask: "",
      idProjeto: 0,
      responseData: null, // Inicialize como nulo
    };
  },
  async mounted() {
    await this.fetchData(); // Primeira chamada
    this.startPesquisa(); // Inicia o loop de consulta constante
  },
  methods: {
    async fetchData() {
      try {
        const response = await this.$axios.get("/data"); //Faz o Get do data completo
        this.responseData = response.data;
      } catch (error) {
        console.error("Erro:", error);
      }
    },
    startPesquisa() {
      // Funcao que faz o loop de consulta em intervalos
      const intervaloPesquisa = 5000; // Intervalo de consulta em milissegundos (5 segundos)
      this.tempoPesquisa = setInterval(this.fetchData, intervaloPesquisa);
    },
    stopPolling() {
      //Responsavel por parar/interromper a pesquisa em loop
      clearInterval(this.tempoPesquisa);
    },
    async adicionarTarefa() {
      const data = {
        title: this.titleTask,
        description: "Descrição da nova task",
        status: "ToDo",
        link: "",
      };
      try {
        const response = await this.$axios.post(
          `/projects/${this.idProjeto}/tasks`,
          data
        ); //Faz o Get do data completo
        this.responseData = response.data;
      } catch (error) {
        console.error("Erro:", error);
      }
    },
  },
  beforeDestroy() {
    this.stopPolling(); // Certifique-se de parar o loop de consulta ao destruir o componente
  },
};
</script>
