<template>
  <main class="conteudo-principal">
      <section>
          <span class="subtitulo-lg cadastre">
              Cadastre o aluno:
          </span>
          <div class="componente-form-table">
    <form action="">
    <label for="btn-cadastrar" class="itens">Nome: </label>
    <input
      autocomplete="off" required
      type="text"
      id="btn-cadastrar"
      name="btn-cadastrar"
      v-model="nome"
      >
      <br><br>
    <label for="btn-cadastrar">Data de nascimento: (Formato AAAA-MM-DD)</label>
    <input
      type="text"
      id="btn-cadastrar"
      name="btn-cadastrar"
      v-model="nascimento"
      >
      <br><br>
    <label for="btn-cadastrar">Instrumento que toca: </label>
    <input
      type="text"
      id="btn-cadastrar"
      name="btn-cadastrar"
      v-model="instrumento"
      >
      <br><br>
      <label for="btn-cadastrar">Professor: </label>
      <select
          name=""
          id=""
          v-model="professor"
          >
              <option value=""></option>
              <option
                v-for="professor in professores_disponiveis"
                :key="professor.id"
                :value="professor.id"
              >
                {{ professor.nome }}
              </option>
          </select>
      <br><br>
    <input
      class="botao"
      type="submit"
      value="Cadastrar"
      @click="cadastrarNovoAluno($event)"
      >
    <br><br>
    </form>
    </div>
    <div>
      <span class="subtitulo-lg cadastre">
        Alunos matriculados:
      </span>
      <table>
        <table>
          <thead>
            <tr>
              <th>ID</th>
              <th>Nome do aluno</th>
              <th>Nascimento</th>
              <th>Instrumento</th>
              <th>Professor</th>
              <th></th>
              <th></th>
            </tr>
          </thead>
      <tbody>
        <tr
          v-for="aluno in alunos"
          :key="aluno.id"
        >
          <td>{{ aluno.id }}</td>
          <td>{{ aluno.aluno }}</td>
          <td>{{ aluno.nascimento }}</td>
          <td>{{ aluno.instrumento }}</td>
          <td>{{ aluno.professor }}</td>
          <td>
            <button class="botao" @click="atualizarAluno(aluno.id)">Alterar</button>
          </td>
          <td>
            <button class="botao" @click="excluirAluno(aluno.id)">Excluir</button>
          </td>
        </tr>
      </tbody>
        </table>
      </table>
    </div>
  </section>
  </main>
</template>

<script>
import axios from 'axios';

export default {
name: 'ConteudoAluno',
data () {
  return {
    nome: '',
    nascimento: '',
    instrumento: '',
    professor: '',
    professores_disponiveis: [],
    alunos: []
  }
},
mounted() {
  this.preencherSelect();
  this.listar();
},
methods: {
  cadastrarNovoAluno(e) {

    e.preventDefault();

    axios
    .post('http://localhost:3000/routes/alunos/cadastrar', {
      "id_professor": this.professor,
      "nome_aluno": this.nome,
    })
    .then(response => {
      console.log(response);
      this.listar()
    })
    .catch(error => console.log(error))
  },
  preencherSelect() {
    axios
      .get('http://localhost:3000/routes/professores/listar')
      .then(response => {

      this.professores_disponiveis = response.data.data
      this.listar()
    })
    .catch(error => console.log(error))
  },
  listar() {
    axios
    .get('http://localhost:3000/routes/alunos/listar')
    .then(response => {

      this.alunos = response.data.data
      this.listar()
      })
      .catch(error => console.log(error))
  },
  atualizarAluno(id) {

    axios
      .get(`http://localhost:3000/routes/alunos/${id}`)
      .then(response => {

        var id_professor = window.prompt("ID do professor:", response.data.data.id_professor);

        if (id_professor !== null) {
          
          var nome = window.prompt("Nome do aluno:", response.data.data.aluno);
          var novo_nascimento = window.prompt("Data de nascimento:", response.data.data.aluno);
          var novo_instrumento = window.prompt("Instrumento:", response.data.data.aluno);

          if (nome !== null) {

            axios
            .patch(`http://localhost:3000/routes/alunos/alterar/${id}`, {
              id_professor: id_professor,
              nome_aluno: nome,
              nascimento: novo_nascimento,
              instrumento: novo_instrumento
            })
            .then(response => {
              console.log(response);
              this.listar();
            })
            .catch(error => console.log(error))

          } else {

            console.log("Você cancelou a operação.");
          }
        } else {
          console.log("Você cancelou a operação.");
        }
      })
      .catch(error => console.log(error))
  },
  excluirAluno(id) {

    console.log(id)

    var result = window.confirm("Você tem certeza que deseja excluir este aluno?");

    if (result) {
      axios
      .delete(`http://localhost:3000/routes/alunos/excluir/${id}`)
      .then(response => {
        console.log(response);
        this.listar()
      })
      .catch(error => console.log(error))
    } else {
      window.alert("O usuário cancelou a exclusão.");
    }
  }
}
}
</script>

<style scoped>
.conteudo-principal {
padding-bottom: 7.5rem;
background: var(--creme, #FFFAF3);
color: var(--cinza, #444);
border-radius: 3.75rem 3.75rem 0rem 0rem;

display: flex;
flex-direction: column;
align-items: center;
gap: 5rem;
}
.cadastre {
color: var(--coral, #F0633C);
display: block;
text-align: center;
margin: 1.5rem;
}
@media only screen and (max-width: 1300px) {
.conteudo-principal {
  padding: 5rem 3.75rem;
  gap: 3.5rem;
}
}
@media only screen and (max-width: 767px) {
.conteudo-principal {
  padding: 4rem 1.5rem;
  gap: 4rem;
}
}
input, select {
margin-left: .5rem;
color: black;
font-size: 15px;
width: 100%;
padding: 10px 5px 5px;
border-radius: 5px;
border-width: 3px;
}
.componente-form-table {
width: 100%;
display: flex;
justify-content: space-evenly;
}
.itens{
  margin-bottom: 1rem;
}
.botao{
margin-left: .5rem;
color: #000000;
font-size: 15px;
width: 30%;
padding: 5px 5px 5px;
border-radius: 4px;
}
table {
  border: 1px solid black;
  border-collapse: collapse;
  background-color: #f5f5f5;
  width: 100%;
  margin-bottom: 20px;
}
th, td {
  padding: 8px;
  text-align: left;
}

th {
  background-color: #333;
  color: #fff;
}
</style>