<template>
  <transition name="modal-fade">
    <div class="modal-backdrop">
      <div
        class="modal"
        role="dialog"
        aria-labelledby="modalTitle"
        aria-describedby="modalDescription"
      >
        <header class="modal-header" id="modalTitle">
          <slot name="header"> Editar Cadastro </slot>
          <button
            type="button"
            class="btn-close"
            @click="close"
            aria-label="Close modal"
          >
            x
          </button>
        </header>
        <section class="modal-body" id="modalDescription">
          <slot name="body">
            <form action="" @submit="createCad">
              <div class="input-container">
                <label for="Name">Nome:</label>
                <input
                  type="text"
                  name="name"
                  id="name"
                  v-model="nome"
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
                  <input
                    type="radio"
                    :value="false"
                    v-model="pessoaJuri"
                    checked
                  />
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
                <input
                  type="submit"
                  value="Atualizar meu Cadastro!"
                  class="submit-btn"
                  @click="close()"
                />
              </div>
            </form>
          </slot>
        </section>
      </div>
    </div>
  </transition>
</template>
<script>
export default {
  name: "Modal",
  props: { pessoaId: Number },
  data() {
    return {
      id: null,
      nome: null,
      sobrenome: null,
      email: null,
      pessoaJuri: false,
      cpf: null,
      cnpj: null,
      controler: false,
    };
  },
  watch: {
    pessoaJuri(newValue, oldValue) {
      this.controler = newValue;
      console.log(this.pessoaId);
    },
  },
  methods: {
    close() {
      this.$emit("close");
    },
    async createCad(e) {
      e.preventDefault();

      const data = {
        nome: this.nome,
        sobrenome: this.sobrenome,
        email: this.email,
        pessoaJuri: this.pessoaJuri,
        cpf: this.cpf,
        cnpj: this.cnpj,
      };

      const dataJson = JSON.stringify(data);

      const req = await fetch(
        `http://localhost:3000/CadCompleto/${this.pessoaId}`,
        {
          method: "PUT",
          headers: { "Content-Type": "application/json" },
          body: dataJson,
        }
      );
      const res = await req.json();
      //limpando os campos
      this.nome = "";
      this.sobrenome = "";
      this.pessoaJuri = false;
      this.cpf = null;
      this.cnpj = null;
      this.email = "";
    },
  },
};
</script>
<style scoped>
.modal-backdrop {
  position: fixed;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  background-color: rgba(0, 0, 0, 0.3);
  display: flex;
  justify-content: center;
  align-items: center;
}

.modal {
  background: #ffffff;
  box-shadow: 2px 2px 20px 1px;
  overflow-x: auto;
  display: flex;
  flex-direction: column;
}

.modal-header,
.modal-footer {
  padding: 1rem;
  display: flex;
}

.modal-header {
  position: relative;
  border-bottom: 1px solid #eeeeee;
  color: #1565c0;
  justify-content: space-between;
}

.modal-footer {
  border-top: 1px solid #eeeeee;
  flex-direction: column;
}

.modal-body {
  position: relative;
  padding: 1.25rem 0.625rem;
}

.btn-close {
  position: absolute;
  top: 0;
  right: 0;
  border: none;
  font-size: 1.25rem;
  padding: 0.625rem;
  cursor: pointer;
  font-weight: bold;
  color: #1565c0;
  background: transparent;
}

.btn-green {
  color: white;
  background: #1565c0;
  border: 1px solid #1565c0;
  border-radius: 2px;
}

.modal-fade-enter,
.modal-fade-leave-to {
  opacity: 0;
}

.modal-fade-enter-active,
.modal-fade-leave-active {
  transition: opacity 0.5s ease;
}
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