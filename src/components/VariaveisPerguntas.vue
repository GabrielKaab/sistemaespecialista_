<template>
    <div>
        <div class="row">
            <div class="col-md-6">
                <h5>Variáveis sem perguntas</h5>
                <h3 v-for="item in variaveis" v-bind:key="item.nome">
                    <span v-if="item.pergunta == ''">
                        <input type="radio" :value="item.nome" @change="pergunta_variavel = item.pergunta" v-model="chosen"> 
                        {{item.nome}}
                    </span>
                </h3>
            </div>
            <div class="col-md-6" >
                <h5>Variáveis com perguntas</h5>
                <h3 v-for="item in variaveis" v-bind:key="item.nome">
                    <span v-if="item.pergunta != ''">
                        <input type="radio" :value="item.nome" @change="pergunta_variavel = item.pergunta" v-model="chosen">
                        {{item.nome}}
                    </span>
                </h3>
            </div>
        </div>
        <br>
        <input type="text" class="form-control" placeholder="Pergunta da variável" v-model="pergunta_variavel"/><br><br>
        <btn  class="btn btn-info form-control" @click="addPerguntaVariavel(chosen, pergunta_variavel)">Ok</btn>
    </div>
</template>
<script>

export default {
    name: 'VariaveisPerguntas',
    data() {
        return {
            chosen: '',
            variaveis_objetivo: []
        }
    },
    props:{
        variaveis: Array,
    },
    mounted(){
    },
    methods: {
        addPerguntaVariavel(chosen, pergunta_variavel){
            var findArray =  this.variaveis.find(obj => { return obj.nome == this.chosen; })
            if(findArray != null){
                findArray.pergunta = pergunta_variavel;
                console.log(chosen);
                
            }else{
               alert('Nananinanão');
            }
        }
    }
}
</script>