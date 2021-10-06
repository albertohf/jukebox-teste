<template>
  <div>
    <Message :msg="msg" v-show="msg" />
    <div>
      <form id="cadastro-form" @submit="createCad">
        <div class="input-container">
          <label for="Name">Nome:</label>
          <input
            type="text"
            name="name"
            id="name"
            v-model="name"
            placeholder="Digite o seu nome"
          />
        </div>
        <div class="input-container">
          <label for="sobrenome">Sobrenome:</label>
          <input
            type="text"
            name="sobrenome"
            id="sobrenome"
            v-model="sobrenome"
            placeholder="Digite o seu sobrenome"
          />
        </div>
        <div class="input-container">
          <label for="sobrenome">Email:</label>
          <input
            type="email"
            name="email"
            id="email"
            v-model="email"
            placeholder="Digite o seu Email"
          />
        </div>

        <div id="options-container" class="input-container">
          <label class="options-title" for="options">Voce é uma:</label>
          <span>
            <input type="radio" :value="false" v-model="pessoaJuri" checked />
            Pessoa Fisica</span
          >
          <span>
            <input type="radio" :value="true" v-model="pessoaJuri" />
            Pessoa Juridica</span
          >
        </div>
        <div class="input-container" v-if="!controler">
          <label for="cpf">CPF:</label>
          <input
            type="text"
            name="cpf"
            pattern="\d{3}\.\d{3}\.\d{3}-\d{2}"
            title="Digite um CPF no formato: xxx.xxx.xxx-xx"
            id="cpf"
            v-model="cpf"
            placeholder="Digite o seu CPF"
          />
        </div>
        <div class="input-container" v-if="controler">
          <label for="cnpj">CNPJ:</label>
          <input
            type="text"
            name="cnpj"
            pattern="\d{2}\.?\d{3}\.?\d{3}/?\d{4}-?\d{2}"
            title="Digite um CNPJ no formato: XX.XXX.XXX/0001-XX"
            id="cnpj"
            v-model="cnpj"
            placeholder="Digite o seu CNPJ"
          />
        </div>
        <div class="input-container">
          <input type="submit" value="Criar meu Cadastro!" class="submit-btn" />
        </div>
      </form>
    </div>
  </div>
</template>
<script>
import Message from "./Message";
export default {
  name: "CadForm",
  components: {
    Message,
  },
  data() {
    return {
      name: null,
      sobrenome: null,
      email: null,
      pessoaJuri: false,
      cpf: null,
      cnpj: null,
      msg: null,
      controler: false,
    };
  },
  watch: {
    pessoaJuri(newValue, oldValue) {
      this.controler = newValue;
    },
  },
  methods: {
    async createCad(e) {
      e.preventDefault();

      const data = {
        nome: this.name,
        sobrenome: this.sobrenome,
        email: this.email,
        pessoaJuri: this.pessoaJuri,
        cpf: this.cpf,
        cnpj: this.cnpj,
      };

      const dataJson = JSON.stringify(data);

      const req = await fetch("http://localhost:3000/CadCompleto", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: dataJson,
      });

      const res = await req.json();

      //limpando os campos
      this.name = "";
      this.sobrenome = "";
      this.pessoaJuri = false;
      this.cpf = null;
      this.cnpj = null;
      this.email = "";

      //msg do sistema
      this.msg = `Cadastro Nº ${res.id} Realizado com sucesso!`;
      // limpar msg sistema
      setTimeout(() => {
        this.msg = "";
      }, 4000);
    },
  },
};
</script>
<style scoped>
#cadastro-form {
  width: 25rem;
  margin: 0 auto;
}
.input-container {
  display: flex;
  flex-direction: column;
  margin-bottom: 1.25rem;
}
label {
  font-weight: bold;
  margin-bottom: 1rem;
  padding: 0.313rem 0.625rem;
  border-left: 4px solid #1565c0;
}

input,
select {
  padding: 0.313rem 0.625rem;
}
.checkbox-container {
  display: flex;
  align-items: flex-start;
  width: 50%;
  margin-bottom: 1.25rem;
}
#options-container {
  flex-direction: column;
  flex-wrap: wrap;
}
.options-title {
  width: 100%;
}

.checkbox-container span,
.checkbox-container input {
  width: auto;
}
.checkbox-container span {
  margin-left: 0.375rem;
  font-weight: bold;
}

.submit-btn {
  background-color: #222;
  color: #1565c0;
  font-weight: bold;
  border: 2px solid #222;
  padding: 0.625rem;
  font-size: 1rem;
  margin: 0 auto;
  cursor: pointer;
  margin-bottom: 1.5rem;
}
.submit-btn:hover {
  background-color: transparent;
  color: #222;
}
span {
  padding: 0 0.825rem;
}
span input {
  padding: 0 1rem;
}
</style>