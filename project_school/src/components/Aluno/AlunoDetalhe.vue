<template>
    <div>
        <titulo :texto="`Aluno: ${aluno.nome}`" :btnVoltar="estaEditando">
            <button v-show="!estaEditando" class="btn btn-editar" @click="permiteEdicao()">Editar</button>
        </titulo>        
        <table>
            <tbody>
                <tr>
                    <td class="colPequeno">Matrícula:</td>
                    <td>
                        <label>{{aluno.id}}</label>
                    </td>
                </tr>
                <tr>
                    <td class="colPequeno">Nome:</td>
                    <td>
                        <label v-if="!estaEditando">{{aluno.nome}}</label>                        
                        <input v-else v-model="aluno.nome" type="text"/>
                    </td>
                </tr>
                <tr>
                    <td class="colPequeno">Sobrenome:</td>
                    <td>
                        <label v-if="!estaEditando">{{aluno.sobrenome}}</label>
                        <input v-else v-model="aluno.sobrenome" type="text"/>
                    </td>
                </tr>
                <tr>
                    <td class="colPequeno">Data nascimento:</td>
                    <td>
                        <label v-if="!estaEditando">{{aluno.dataNasc}}</label>
                        <input v-else v-model="aluno.dataNasc" type="text"/>
                    </td>
                </tr>
                <tr>
                    <td class="colPequeno">Professor:</td>
                    <td>
                        <label v-if="!estaEditando">{{aluno.professor.nome}}</label>
                        <select v-else v-model="aluno.professor" name="" id="">
                            <option v-for="(professor, index) in professores" :key="index" :value="professor">
                                {{professor.nome}}
                            </option>  
                        </select>
                    </td>
                </tr>
            </tbody>
        </table>

        <div style="margin-top: 10px"> 
            <div v-if="estaEditando">
                <button class="btn btn-salvar" @click="salvar(aluno)">Salvar</button>
                <button class="btn btn-cancelar" @click="cancelar()">Cancelar</button>

            </div>
        </div>
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
                professores: [],
                aluno: {},
                idAluno: this.$route.params.id,
                estaEditando: false
            }
        },
        created() {
            this.$http.get(`http://localhost:3000/alunos/${this.idAluno}`)
                .then(res => res.json())
                .then(aluno => this.aluno = aluno);

            this.$http.get('http://localhost:3000/professores')
              .then(res => res.json())
              .then(professor => this.professores = professor);
        },
        methods: {
            permiteEdicao() {
                this.estaEditando = !this.estaEditando;
            },
            salvar(_aluno) {
                let _alunoEditar = {
                    id: _aluno.id,
                    nome: _aluno.nome,
                    sobrenome: _aluno.sobrenome,
                    dataNasc: _aluno.dataNasc,
                    professor: _aluno.professor
                }

                this.$http
                    .put(`http://localhost:3000/alunos/${_alunoEditar.id}`, _alunoEditar);
                
                this.estaEditando = !this.estaEditando;
            },
            cancelar() {
                this.estaEditando = !this.estaEditando;
            }
        }
    }
</script>

<style scoped>
.btn-editar {
    float: right;
    background-color: rgb(76, 186, 249);
}
.btn-salvar {
    float: right;
    background-color: rgb(79, 196, 68);
}
.btn-cancelar {
    float: left;
    background-color: rgb(249, 186, 92);
}
.colPequeno {
    width: 20%;    
}
input, select {
    margin: 0px;
    padding: 5px 10px;
    font-size: 0.9em;
    font-family: Montserrat;
    border-radius: 3px;
    border: 1px solid silver; 
    background-color: rgb(245, 245, 245);
    width: 95%;   
}
select {
    height: 38px;
    width: 100%;
}
</style>