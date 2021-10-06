<template>
  <Message :msg="msg" v-show="msg" />
  <div class="container">
    <table>
      <tr id="header">
        <th>Nome Completo</th>
        <th>Email</th>
        <th>Pessoa Juridica</th>
        <th>CPF / CNPJ</th>
        <th></th>
        <th></th>
      </tr>
      <tr v-for="(cad, index) in pessoa" :key="cad.id">
        <td>{{ toFullName(index) }}</td>
        <td>{{ cad.email }}</td>
        <td>{{ cad.pessoaJuri ? "Sim" : "Não" }}</td>
        <td>{{ cad.cpf ? cad.cpf : cad.cnpj }}</td>
        <td>
          <button class="delete-btn" @click="showModal(cad.id)">Editar</button>
        </td>
        <td>
          <button class="delete-btn" @click="deleteCad(cad.id)">Deletar</button>
        </td>
      </tr>
    </table>
    <Modal v-show="isModalVisible" @close="closeModal" :pessoaId="pessoaId" />
  </div>
</template>
<script>
import Message from "./Message";
import Modal from "./Modal";
export default {
  name: "Dashboard",
  components: {
    Message,
    Modal,
  },
  data() {
    return {
      pessoa: null,
      pessoaId: null,
      msg: null,
      isModalVisible: false,
    };
  },
  computed: {
    fullName() {
      return this.pessoa[0].nome + " " + this.pessoa[0].sobrenome;
    },
    //não consegui passar parametros para as propriedades computadas sem erros.
  },
  methods: {
    showModal(person) {
      this.pessoaId = person;
      this.isModalVisible = true;
    },
    closeModal() {
      this.isModalVisible = false;
    },
    toFullName(id) {
      const fullName = this.pessoa[id].nome + " " + this.pessoa[id].sobrenome;
      return fullName;
    },
    async getRequests() {
      const req = await fetch("http://localhost:3000/CadCompleto");

      const data = await req.json();

      this.pessoa = data;
    },
    async deleteCad(id) {
      const req = await fetch(`http://localhost:3000/CadCompleto/${id}`, {
        method: "DELETE",
      });
      const res = await req.json();
      //msg

      this.getRequests();

      //msg do sistema
      this.msg = `Pedido Nº ${id} deletado com sucesso!`;
      // limpar msg sistema
      setTimeout(() => {
        this.msg = "";
      }, 4000);
    },
  },
  mounted() {
    this.getRequests();
  },
};
</script>
<style scoped>
.delete-btn {
  background-color: #222;
  color: #1565c0;
  font-weight: bold;
  border: 2px solid #222;
  padding: 0.625rem;
  font-size: 1rem;
  margin: 0 auto;
  cursor: pointer;
  transition: 0.5s;
}

.delete-btn:hover {
  background-color: transparent;
  color: #222;
}
.container {
  padding: 0px;
  margin: 0;
  font-family: "Helvetica" sans-serif;
}

table {
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  border-collapse: collapse;
  width: 1000px;
  height: 200px;
  border: 1px solid #bdc3c7;
  box-shadow: 2px 2px 12px rgba(0, 0, 0, 0.2), -1px -1px 8px rgba(0, 0, 0, 0.2);
}

tr {
  transition: all 0.2s ease-in;
  cursor: pointer;
}

th,
td {
  padding: 12px;
  text-align: left;
  border-bottom: 1px solid #ddd;
}

#header {
  background-color: #1565c0;
  color: #fff;
}

h1 {
  font-weight: 600;
  text-align: center;
  background-color: #1565c0;
  color: #fff;
  padding: 10px 0px;
}

tr:hover {
  background-color: #f5f5f5;
  transform: scale(1.02);
  box-shadow: 2px 2px 12px rgba(0, 0, 0, 0.2), -1px -1px 8px rgba(0, 0, 0, 0.2);
}

@media only screen and (max-width: 768px) {
  table {
    width: 90%;
  }
}
</style>
