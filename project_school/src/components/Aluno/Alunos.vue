<template>
  <div>
    <titulo :texto="professorId != undefined ? 'Professor: ' + professor.nome : 'Todos os alunos'"/>
    <div v-if="professorId != undefined">
      <input type="text" placeholder="Nome do aluno" v-model="nome"
      @keyup.enter="addAluno()">
      <button class="btn btnInput" @click="addAluno()">Adicionar</button>
    </div>   

     <table>
       <thead>
         <th>Mat.</th>
         <th>Nome</th>
         <th>Opções</th>
       </thead>
       <tbody v-if="alunos.length">
         <tr v-for="(aluno, index) in alunos" :key="index">
           <td class="colPequeno">{{aluno.id}}</td>
           <router-link :to="`/alunodetalhe/${aluno.id}`" tag="td" style="cursor: pointer">
             {{aluno.nome}} {{aluno.sobrenome}}
           </router-link>
           <td class="colPequeno">
             <button class="btn btn-danger" @click="remover(aluno)">Remover</button>
           </td>
         </tr>
       </tbody>
       <tfoot v-else>
         <br>
         Nunhum aluno encontrado.
       </tfoot>
     </table>   
  </div>
</template>

<script>
import Titulo from '../_share/Titulo'

export default {
  components: {
    Titulo
  },
  data() {
    return {
      titulo: 'Aluno',
      professorId: this.$route.params.prof_id,
      professor: {
      },
      nome: '',
      alunos: []
    }
  },
  created() {    
    if (this.professorId) {
      this.carregarProfessores();
      this.$http.get(`http://localhost:3000/alunos?professor.id=${this.professorId}`)
        .then(res => res.json())
        .then(alunos => this.alunos = alunos)
    } else {
      this.$http.get('http://localhost:3000/alunos')
        .then(res => res.json())
        .then(alunos => this.alunos = alunos)
    }    
  },
  props: {
  },
  methods: {
    addAluno() {
      let _aluno = {
        nome: this.nome,
        sobrenome: "",
        professor: {
          id: this.professor.id,
          nome: this.professor.nome
        },
      }

      this.$http.post('http://localhost:3000/alunos', _aluno)
      .then(res => res.json())
      .then(alunoResponse => {
        this.alunos.push(alunoResponse);
        this.nome = '';
      })      
    },
    remover(aluno) {
      this.$http.delete(`http://localhost:3000/alunos/${aluno.id}`)
      .then(() => {
        let indice = this.alunos.indexOf(aluno);
        this.alunos.splice(indice, 1);
      })      
    },
    carregarProfessores() {
      this.$http.get('http://localhost:3000/professores/' + this.professorId)
        .then(res => res.json())
        .then(professor => {
          this.professor = professor;
        })
    }
  },
}
</script>

<style scoped>
input {
  width: calc(100% - 195px);
  border: 0;
  padding: 20px;
  font-size: 1.3em;
  color: #687f7f;  
  display: inline;
}
.btnInput {
  width: 150px;
  border: 0;
  padding: 20px;
  font-size: 1.3em;
  display: inline;
  background-color: rgb(116, 115, 115);
}

</style>
