<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <div class="container">
      <b-modal ref="variavelobj-modal" hide-footer title="Cadastro de Variáveis-Objetivo">
        <VariaveisObjetivo :variaveis="this.variavel"></VariaveisObjetivo>
      </b-modal>
      <b-modal ref="variavelperguntas-modal" hide-footer title="Cadastro de Perguntas">
        <VariaveisPerguntas :variaveis="this.variavel"></VariaveisPerguntas>
      </b-modal>

      <b-modal class="glass-card" ref="regras-modal" hide-footer title="Cadastro de Regras">
          {{'Regra: ' + this.numRegra}}
          <p>Se</p>
          <div class="row">
            <div class="col-md-6">
              <select class="form-control" @change="getValores(var_selected)" v-model="var_selected">
                  <option v-for="item in variavel"  v-bind:key="item.nome" >{{item.nome}}</option>
              </select>
            </div>
            =
            <div class="col-md-5">
              <select class="form-control" v-model="val_selected">
                  <option v-for="item in valores_variavel" v-bind:key="item">{{item}}</option>
              </select>
            </div>
          </div>
          <div v-if="this.regras != ''">
              <input type="radio" id="condition" name="condition" v-model="condicional" value="1"/>E
              <input type="radio" id="condition" name="condition" v-model="condicional" value="2"/>Ou
          </div>
          <br>
          <p>Então</p>
          <div class="row">
            <div class="col-md-6">
              <select class="form-control" @change="getValores2(entao_var_selected)" v-model="entao_var_selected">
                  <option v-for="item in variavel" v-show="item.tipo == 2" v-bind:key="item.nome" >{{item.nome}}</option>
              </select>
            </div>
            =
            <div class="col-md-5">
              <select class="form-control" v-model="entao_val_selected">
                <option v-for="item2 in this.valores_variavel2" v-bind:key="item2">{{item2}}</option>
              </select>
            </div>
          </div>
          <br><br>
          <div style="text-align: center !important">
            <btn class="btn btn-purple" @click="concludeRegra(var_selected, val_selected, condicional,entao_var_selected, entao_val_selected)">Concluir</btn>
            <br><br>
            <btn class="btn btn-info" @click="nextRegra()">Próxima Regra</btn>
          </div>
          <p v-for="item in this.regras" v-bind:key="item.regraNum">
            <code>
              {{'Regra ' + item.regraNum + ': Se ' + item.var + ' = ' + item.valor}}
              <span v-for="andcondition in item.e" v-bind:key="andcondition.var">
              {{'e ' + andcondition.var + ' = ' + andcondition.valor}} 
              </span>
              {{'então ' + item.entao_var + ' = ' + item.entao_valor}}
            </code>
          </p>   
        </b-modal>
    <div class="row">
      <!-- Variaveis-->
      <div class="col-md-2">
        <btn class="btn btn-purple form-control" style="margin-bottom: 10px" @click="newProject()">Novo Projeto</btn><br>
        <btn class="btn btn-purple form-control" style="margin-bottom: 10px" @click="showModalProject()">Abrir Projeto</btn><br>
        <b-button class="form-control" style="margin-bottom: 10px" variant="purple" @click="showModalVariavel()">Variáveis</b-button><br>
        <b-button class="form-control" style="margin-bottom: 10px" variant="purple" @click="showModalVariavelObj()">Variáveis-Objetivo</b-button><br>
        <b-button class="form-control" style="margin-bottom: 10px" variant="purple" @click="showModalVariavelPerguntas()">Cadastro de Perguntas</b-button><br>
        <b-button class="form-control" style="margin-bottom: 10px" variant="purple" @click="showModalRegras()">Cadastro de Regras</b-button>

        <btn class="form-control btn btn-purple" @click="loadCerveja()">Teste cerveja</btn>

        <b-modal ref="projeto-modal" hide-footer title="Projetos">
          <input type="text" class="form-control" placeholder="Nome do Projeto" v-model="nome_projeto"/>
          <br>
          <input type="text" class="form-control" placeholder="Responsável" v-model="responsavel_projeto"/>
          <br>
          <btn class="btn btn-purple form-control" @click="createProject(nome_projeto, responsavel_projeto)">Adicionar</btn>
          <br><br>
          <table class="table" style="width:100%">
            <tr>
              <th>Nome</th>
              <th>Responsável</th>
            </tr>
            <tr v-for="item in projetos" v-bind:key="item.nome">
              <td>{{item.nome}}</td>
              <td>{{item.responsavel}}</td>
              <td><btn class="btn btn-purple form-control" @click="openProject(item)">Abrir Projeto</btn></td>
            </tr>
          </table>
        </b-modal>

        <b-modal ref="variavel-modal" hide-footer title="Cadastro de Variáveis">
          <input type="text" class="form-control" placeholder="Nome da variável" v-model="nome_variavel"/>
          <br>
          <input type="text" class="form-control" placeholder="Valor" v-model="valor_variavel"/>
          <br>
          <btn class="btn btn-purple form-control" @click="criarVariavel(nome_variavel, valor_variavel)">Adicionar</btn>
          <br><br>
          <table class="table" style="width:100%">
            <tr>
              <th>Nome</th>
              <th>Valor</th>
            </tr>
            <tr v-for="item in variavel" v-bind:key="item.nome">
              <td>{{item.nome}}</td>
              <td>{{item.valor}}</td>
            </tr>
          </table>
        </b-modal>
      </div>
      <div class="col-md-2"></div>
      <div class="col-md-4">
        <br><br>
        <div>
          <div v-for="item in variavel" v-bind:key="item" style="margin-bottom: 20px; color: white">
            <div v-if="item.tipo != 2" class="glass-card">
              <p style="font-size: 20px">{{item.pergunta}}</p>
              <span v-for="valor in item.valor" v-bind:key="valor.nome">
                <input type="radio" id="answer" name="answer" @change="selectResposta(resposta)" v-model="resposta" :value="valor">{{valor}}
              </span>
              <br><br>
              <btn class="btn btn-success" @click="nextQuestion(item)">Marcar</btn>
              <br><br>
            </div>           
          </div>
          <btn class="btn btn-success" @click="concludeForm()">Concluir Form</btn>
          <p style="color: red; font-size: 30px">{{this.exibir}}</p>
        </div>
      </div>
    </div>
    <br>

    </div>
  </div>
</template>

<script>

import VariaveisObjetivo from './components/VariaveisObjetivo.vue'
import VariaveisPerguntas from './components/VariaveisPerguntas.vue'

export default {
  name: 'App',
  components: {
    VariaveisObjetivo,
    VariaveisPerguntas,
  },
  data() {
    return {
      variavel: [],
      nome_variavel: '',
      valor_variavel: '',
      var_selected: '',
      val_selected: '',
      condicional: 0,
      entao_var_selected: '',
      entao_val_selected: '',
      valores_variavel2: '',
      valores_variavel: [],
      regra: [],
      regras: [],
      form_respostas: [],
      form_resposta: '',
      exibir: '',
      resposta: '',
      projetos: [],
      numRegra: 0,
      pagination: 1,
      showForm: false,
      nome_projeto: '',
      responsavel_projeto: ''
    }
  },
  methods: {
    newProject(){
      this.variavel = [];
      this.regras = [];
    },
    openProject(item){
      this.variavel = item.variaveis;
      this.regras = item.regras;
    },
    createProject(nome_projeto, responsavel_projeto){
      var objectProject = {
        variaveis: this.variavel,
        regras: this.regras,
        nome: nome_projeto,
        responsavel: responsavel_projeto
      }
      this.projetos.push(objectProject);
      console.log(this.projetos);
    },
    concludeForm(){
      this.regras.forEach(regra => {
        var respostax = this.form_respostas.find(obj => {
          return obj.variavel == regra.var
          });

        var validacao = (respostax.resposta == regra.valor);

        if(regra.condicional != 1){
          if(validacao){
            this.exibir = regra.entao_valor;
          }
        }else{
          regra.condicional.forEach(element => {
            var entradaE = this.regras.find(obj => {return obj.variavel == element.var});

            var validacaoE = (entradaE.resposta == element.valor);

            validacao = (validacao && validacaoE);
          })

          if(validacao){
            this.exibir = regra.entao_valor;
          }
        }
      });
     
    },
    selectResposta(resposta){
      console.log(resposta);
      this.form_resposta = resposta;
      console.log(this.form_resposta);
    },
    nextQuestion(item){
        var form = {
          variavel: item.nome,
          resposta: this.form_resposta,
        }
        this.form_respostas.push(form);
    },
    criarVariavel(nome_variavel, valor_variavel) {
      var findArray =  this.variavel.find(obj => { return obj.nome == this.nome_variavel; })
      if(findArray != null){
        findArray.valor.push(valor_variavel);
        findArray.tipo.push(1);  
        findArray.pergunta.push('');
      }else{
        var objectVariavel = {
            nome: nome_variavel,
            valor: [valor_variavel],
            tipo: 1,
            pergunta: ''
        }
        this.variavel.push(objectVariavel);
      }
    },
    getValores(var_selected){
      var findArray =  this.variavel.find(obj => { return obj.nome == var_selected; })
      this.valores_variavel = findArray.valor;
    },
    getValores2(entao_var_selected){
      var findArray2 =  this.variavel.find(obj2 => { return obj2.nome == entao_var_selected; })
      this.valores_variavel2 = findArray2.valor;
    },
    concludeRegra(var_selected, val_selected, condicional, entao_var_selected, entao_val_selected){
      var objectRegra;

      var condicao = this.regras.find(obj => {
        console.log(obj);
        console.log(this.numRegra);
        return obj.regraNum == this.numRegra
      });

      console.log(condicao);

      if(condicao == null || condicao === undefined ){
        if(condicional == 1){
          objectRegra = {
            regraNum: this.numRegra,
            condicional: condicional,
            e: [
            {
                var: this.var_selected,
                valor: this.val_selected
            }
          ],
            entao_var: entao_var_selected,
            entao_valor: entao_val_selected
          }
          this.regras.push(objectRegra);
        }else{
          objectRegra = {
            regraNum: this.numRegra,
            var: var_selected,
            valor: val_selected,
            condicional: condicional,
            e: [],
            entao_var: entao_var_selected,
            entao_valor: entao_val_selected
          }
          this.regras.push(objectRegra);
        }
      }else{
        if(condicional == 1){

          objectRegra = {
            var: this.var_selected,
            valor: this.val_selected
          };
          
          condicao.e.push(objectRegra);
          
        }
      }
      console.log(this.regras);
    },
    nextRegra(){
      ++this.numRegra;
      this.var_selected = null;
      this.val_selected = null;
      this.condicional = null;
      this.entao_var_selected = null; 
      this.entao_val_selected = null;
    },
    showModalProject(){
      this.$refs['projeto-modal'].show();
    },
    showModalVariavel(){
      this.$refs['variavel-modal'].show();
    },
    showModalVariavelObj(){
      this.$refs['variavelobj-modal'].show();
    },
    showModalVariavelPerguntas(){
      this.$refs['variavelperguntas-modal'].show();
    },
    showModalRegras(){
      this.$refs['regras-modal'].show();
    },
    toggleFormOn(){
      this.showForm = true;
    },
    loadCerveja(){
      var cerveja = {
        nome: 'Cerveja',
        valor: ['Ales: America Pale Ale', 'Ales: Porter', 'Ales: Stout', 'Ales: India Pale Ale', 'Ales: Weiss', 'Lager: Pilsen',
        'Lager: Dunkel', 'Lages: Amber Lager', 'Lager: Malzbier', 'Lager: Bock'],
        tipo: 2,
        pergunta: ''
      }
      this.variavel.push(cerveja);

      var aroma = {
        nome: 'Aroma',
        valor: ['Suave', 'Intenso'],
        tipo: 1,
        pergunta: 'Você prefere aromas intensos ou suaves?'
      }
      this.variavel.push(aroma);

      var cor = {
        nome: 'Cor',
        valor: ['Clara', 'Dourada', 'Escura'],
        tipo: 1,
        pergunta: 'Prefere cervejas claras, douradas ou escuras?'
      }
      this.variavel.push(cor);

      var teoralcolico = {
        nome: 'Teor Alcolico',
        valor: ['Alto', 'Médio', 'Baixo'],
        tipo: 1,
        pergunta: 'Qual o teor alcoolico permitido para o momento?'
      }
      this.variavel.push(teoralcolico);

      var sabor = {
        nome: 'Sabor',
        valor: ['Doce', 'Amargo'],
        tipo: 1,
        pergunta: 'Gostaria de uma cerveja de sabor doce ou amargo?'
      }
      this.variavel.push(sabor);

      var experiencia = {
        nome: 'Experiencia',
        valor: ['Sim', 'Não'],
        tipo: 1,
        pergunta: 'Você já experimentou alguma cerveja artesanal?'
      }
      this.variavel.push(experiencia);

      var saborbanana = {
        nome: 'Sabor Banana',
        valor: ['Sim', 'Não'],
        tipo: 1,
        pergunta: 'Você gostaria de degustar uma cerveja com leve sabor de banana e cravo?'
      }
      this.variavel.push(saborbanana);

      var saborcaramelo = {
        nome: 'Sabor Caramelo',
        valor: ['Sim', 'Não'],
        tipo: 1,
        pergunta: 'Você gosta de uma cerveja com leve sabor de caramelo?'
      }
      this.variavel.push(saborcaramelo);

      var saborchocolate = {
        nome: 'Sabor Chocolate',
        valor: ['Sim', 'Não'],
        tipo: 1,
        pergunta: 'Você acha que chocolate e cerveja combinam?'
      }
      this.variavel.push(saborchocolate);

      var saborcitrico = {
        nome: 'Sabor Citrico',
        valor: ['Sim', 'Não'],
        tipo: 1,
        pergunta: 'Gosta de sabores cítricos?'
      }
      this.variavel.push(saborcitrico);

      var regra0 = {
        regraNum: 0,
        var: 'Experiencia',
        valor: 'Sim',
        e: [{var: "Aroma", valor: "Intenso"},
        {var: "Teor Alcolico", valor: "Baixo"},
        {var: "Sabor Caramelo", valor: "Não"},
        ],
        entao_var: "Cerveja",
        entao_valor: "Lager: Pilsen"
      }
      this.regras.push(regra0);

      var regra1 = {
        regraNum: 1,
        var: 'Experiencia',
        valor: 'Sim',
        e: [{var: "Aroma", valor: "Intenso"},
        {var: "Teor Alcolico", valor: "Baixo"},
        {var: "Sabor Caramelo", valor: "Sim"},
        ],
        entao_var: "Cerveja",
        entao_valor: "Lager: Malzbier"
      }
      this.regras.push(regra1);

      var regra2 = {
        regraNum: 2,
        var: 'Experiencia',
        valor: 'Sim',
        e: [{var: "Aroma", valor: "Intenso"},
        {var: "Teor Alcolico", valor: "Alto"},
        ],
        entao_var: "Cerveja",
        entao_valor: "Ales: Stout"
      }
      this.regras.push(regra2);

      var regra3 = {
        regraNum: 3,
        var: 'Experiencia',
        valor: 'Sim',
        e: [{var: "Aroma", valor: "Intenso"},
        {var: "Teor Alcolico", valor: "Médio"},
        {var: "Sabor", valor: "Doce"},
        {var: "Sabor Banana", valor: "Sim"},
        ],
        entao_var: "Cerveja",
        entao_valor: "Ales: Weiss"
      }
      this.regras.push(regra3);

      var regra4 = {
        regraNum: 4,
        var: 'Experiencia',
        valor: 'Sim',
        e: [{var: "Aroma", valor: "Intenso"},
        {var: "Teor Alcolico", valor: "Médio"},
        {var: "Sabor", valor: "Doce"},
        {var: "Sabor Banana", valor: "Não"},
        ],
        entao_var: "Cerveja",
        entao_valor: "Ales: Stout"
      }
      this.regras.push(regra4);

      var regra5 = {
        regraNum: 5,
        var: 'Experiencia',
        valor: 'Sim',
        e: [{var: "Aroma", valor: "Intenso"},
        {var: "Teor Alcolico", valor: "Médio"},
        {var: "Sabor", valor: "Amarga"},
        {var: "Cor", valor: "Escura"},
        ],
        entao_var: "Cerveja",
        entao_valor: "Ales: Porter"
      }
      this.regras.push(regra5);

      var regra6 = {
        regraNum: 6,
        var: 'Experiencia',
        valor: 'Sim',
        e: [{var: "Aroma", valor: "Intenso"},
        {var: "Teor Alcolico", valor: "Médio"},
        {var: "Sabor", valor: "Amarga"},
        {var: "Cor", valor: "Clara"},
        {var: "Sabor Chocolate", valor: "Sim"},
        ],
        entao_var: "Cerveja",
        entao_valor: "Ales: America Pale Ale"
      }
      this.regras.push(regra6);

      var regra7 = {
        regraNum: 7,
        var: 'Experiencia',
        valor: 'Sim',
        e: [{var: "Aroma", valor: "Intenso"},
        {var: "Teor Alcolico", valor: "Médio"},
        {var: "Sabor", valor: "Amarga"},
        {var: "Cor", valor: "Clara"},
        {var: "Sabor Chocolate", valor: "Não"},
        {var: "Sabor Citrico", valor: "Sim"},
        ],
        entao_var: "Cerveja",
        entao_valor: "Ales: India Pale Ale"
      }
      this.regras.push(regra7);

      var regra8 = {
        regraNum: 8,
        var: 'Experiencia',
        valor: 'Sim',
        e: [{var: "Aroma", valor: "Intenso"},
        {var: "Teor Alcolico", valor: "Médio"},
        {var: "Sabor", valor: "Amarga"},
        {var: "Cor", valor: "Clara"},
        {var: "Sabor Chocolate", valor: "Não"},
        {var: "Sabor Citrico", valor: "Sim"},
        ],
        entao_var: "Cerveja",
        entao_valor: "Ales: India Pale Ale"
      }
      this.regras.push(regra8);

      var regra9 = {
        regraNum: 9,
        var: 'Experiencia',
        valor: 'Sim',
        e: [{var: "Aroma", valor: "Suave"},
        {var: "Teor Alcolico", valor: "Médio"},
        ],
        entao_var: "Cerveja",
        entao_valor: "Lager: Dunkel"
      }
      this.regras.push(regra9);

      var regra10 = {
        regraNum: 10,
        var: 'Experiencia',
        valor: 'Não',
        e: [{var: "Teor Alcolico", valor: "Médio"},
        ],
        entao_var: "Cerveja",
        entao_valor: "Lager: Dunkel"
      }
      this.regras.push(regra10);

      var regra11 = {
        regraNum: 11,
        var: 'Experiencia',
        valor: 'Sim',
        e: [{var: "Aroma", valor: "Suave"},
        {var: "Teor Alcolico", valor: "Alto"},
        ],
        entao_var: "Cerveja",
        entao_valor: "Lager: Bock"
      }
      this.regras.push(regra11);

      var regra12 = {
        regraNum: 12,
        var: 'Experiencia',
        valor: 'Não',
        e: [{var: "Teor Alcolico", valor: "Alto"},
        ],
        entao_var: "Cerveja",
        entao_valor: "Lager: Bock"
      }
      this.regras.push(regra12);

      var regra13 = {
        regraNum: 13,
        var: 'Experiencia',
        valor: 'Sim',
        e: [{var: "Teor Alcolico", valor: "Baixo"},
        {var: "Aroma", valor: "Suave"},
        {var: "Sabor", valor: "Amargo"},
        ],
        entao_var: "Cerveja",
        entao_valor: "Lager: Pilsen"
      }
      this.regras.push(regra13);

      var regra14 = {
        regraNum: 14,
        var: 'Experiencia',
        valor: 'Não',
        e: [{var: "Teor Alcolico", valor: "Baixo"},
        {var: "Sabor", valor: "Amargo"},
        ],
        entao_var: "Cerveja",
        entao_valor: "Lager: Pilsen"
      }
      this.regras.push(regra14);

      var regra15 = {
        regraNum: 15,
        var: 'Experiencia',
        valor: 'Sim',
        e: [{var: "Aroma", valor: "Suave"},
          {var: "Teor Alcolico", valor: "Baixo"},
        {var: "Sabor", valor: "Doce"},
        {var: "Sabor Caramelo", valor: "Não"},
        ],
        entao_var: "Cerveja",
        entao_valor: "Lages: Amber Lager"
      }
      this.regras.push(regra15);

      var regra16 = {
        regraNum: 16,
        var: 'Experiencia',
        valor: 'Sim',
        e: [{var: "Aroma", valor: "Suave"},
          {var: "Teor Alcolico", valor: "Baixo"},
        {var: "Sabor", valor: "Doce"},
        {var: "Sabor Caramelo", valor: "Sim"},
        ],
        entao_var: "Cerveja",
        entao_valor: "Lager: Malzbier"
      }
      this.regras.push(regra16);

      var regra17 = {
        regraNum: 17,
        var: 'Experiencia',
        valor: 'Não',
        e: [{var: "Teor Alcolico", valor: "Baixo"},
        {var: "Sabor", valor: "Doce"},
        {var: "Sabor Caramelo", valor: "Sim"},
        ],
        entao_var: "Cerveja",
        entao_valor: "Lages: Amber Lager"
      }
      this.regras.push(regra17);
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.glass-card{
  /* From https://css.glass */
background: rgba(255, 255, 255, 0.2);
border-radius: 16px;
box-shadow: 0 4px 30px rgba(0, 0, 0, 0.1);
backdrop-filter: blur(6.4px);
-webkit-backdrop-filter: blur(6.4px);
border: 1px solid rgba(255, 255, 255, 0.3);
padding: 20px;
}

div#__BVID__6___BV_modal_content_ {
  background: rgba(255, 255, 255, 0.2);
  border-radius: 16px;
  box-shadow: 0 4px 30px rgb(0 0 0 / 10%);
  backdrop-filter: blur(6.4px);
  -webkit-backdrop-filter: blur(6.4px);
  border: 1px solid rgba(255, 255, 255, 0.3);
}

div#__BVID__5___BV_modal_content_ {
  background: rgba(255, 255, 255, 0.2);
  border-radius: 16px;
  box-shadow: 0 4px 30px rgb(0 0 0 / 10%);
  backdrop-filter: blur(6.4px);
  -webkit-backdrop-filter: blur(6.4px);
  border: 1px solid rgba(255, 255, 255, 0.3);
}

div#__BVID__4___BV_modal_content_ {
  background: rgba(255, 255, 255, 0.2);
  border-radius: 16px;
  box-shadow: 0 4px 30px rgb(0 0 0 / 10%);
  backdrop-filter: blur(6.4px);
  -webkit-backdrop-filter: blur(6.4px);
  border: 1px solid rgba(255, 255, 255, 0.3);
}

div#__BVID__8___BV_modal_content_ {
  background: rgba(255, 255, 255, 0.2);
  border-radius: 16px;
  box-shadow: 0 4px 30px rgb(0 0 0 / 10%);
  backdrop-filter: blur(6.4px);
  -webkit-backdrop-filter: blur(6.4px);
  border: 1px solid rgba(255, 255, 255, 0.3);
}

div#__BVID__7___BV_modal_content_ {
  background: rgba(255, 255, 255, 0.2);
  border-radius: 16px;
  box-shadow: 0 4px 30px rgb(0 0 0 / 10%);
  backdrop-filter: blur(6.4px);
  -webkit-backdrop-filter: blur(6.4px);
  border: 1px solid rgba(255, 255, 255, 0.3);
}

.btn-purple{
  color: #FFF !important;
  background-color: #800080 !important;
  border-color: #800080 !important;
  border-radius: 10px !important;
}

.btn-purple:hover{
  color: #FFF !important;
  background-color: #580058 !important;
  border-color: #580058 !important;
}

</style>
