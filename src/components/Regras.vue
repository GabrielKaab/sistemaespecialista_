<template>
    <div v-if="variaveis != null">
        <h4>Perguntas das variáveis</h4>
        <p>Se</p>
        <select @change="getValores(var_selected)" v-model="var_selected">
            <option v-for="item in variaveis"  v-bind:key="item.nome" >{{item.nome}}</option>
        </select>

        <select v-model="val_selected">
            <option v-for="item in valores_variavel" v-bind:key="item">{{item}}</option>
        </select>

        <input type="checkbox" v-model="not_regra" value="!"/>Não

        <div v-if="this.regra != ''">
            <input type="radio" id="condition" name="condition" v-model="condicional" value="&&"/>E
            <input type="radio" id="condition" name="condition" v-model="condicional" value="||"/>Ou
        </div>
        <br><br>

        <p>Então</p>
        <select @change="getValores(entao_var_selected)" v-model="entao_var_selected">
            <option v-for="item in variaveis"  v-bind:key="item.nome" >{{item.nome}}</option>
        </select>

        <select v-model="entao_val_selected">
            <option v-for="item in valores_variavel" v-bind:key="item">{{item}}</option>
        </select>

        <br><br>
        <btn class="btn btn-info" @click="concludeRegra(entao_var_selected, entao_val_selected)">Concluir</btn>

        <p>Regra {{this.regra}}</p>
        <p>Regras: {{this.regras}}</p>    
    </div>
</template>
<script>

export default {
    name: 'Regras',
    data() {
        return {
            chosen: '',
            valores_variavel: [],
            regra: [],
            regras: [],
        }
    },
    props:{
        variaveis: Array,
    },
    mounted(){
    },
    methods: {
        getValores(var_selected){
            var findArray =  this.variaveis.find(obj => { return obj.nome == var_selected; })
            this.valores_variavel = findArray.valor;
            console.log(this.valores_variavel);
        },
        concludeRegra(var_selected, val_selected, not_regra, condicional, entao_var_selected, entao_val_selected){
            var objectRegra = {
                    not: not_regra,
                    var: var_selected,
                    valor: val_selected,
                    condicional: condicional,
                    entao_var: entao_var_selected,
                    entao_valor: entao_val_selected
                }

            this.regra.push(objectRegra);
            this.regras.push(this.regra);
            this.regra = [];
        }
    }
}
</script>