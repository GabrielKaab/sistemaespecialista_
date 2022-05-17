<template>
    <div>
        <div class="row">
            <div class="col-md-6">
                <h4>Variáveis</h4>
                <h3 v-for="item in variaveis" v-bind:key="item.nome">
                    <span v-if="item.tipo == 1">
                        <input type="radio" :value="item.nome" v-model="chosen"> 
                        {{item.nome}}
                    </span>
                </h3>
            </div>
            <div class="col-md-6" >
                <h4>Variáveis-Objetivo</h4>
                <h3 v-for="item in variaveis" v-bind:key="item.nome">
                    <span v-if="item.tipo == 2">
                        <input type="radio" :value="item.nome" v-model="chosen">
                    {{item.nome}}
                    </span>
                </h3>
            </div>
        </div>
        <br>
        <div class="row">
            <div class="col-md-6">
                <btn class="btn btn-info form-control" @click="addVariavelObjetivo(chosen)">Add</btn>
            </div>
            <div class="col-md-6">
                <btn class="btn btn-danger form-control" @click="removeVariavelObjetivo(chosen)">Remove</btn>
            </div>
        </div>
    </div>
</template>
<script>

export default {
    name: 'VariaveisObjetivo',
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
        this.getvariaveisObjetivo();
    },
    methods: {
        getvariaveisObjetivo(){
            var objetivo = this.variaveis.find(obj => { return obj.tipo == 2; })
            console.log(objetivo);
        },
        addVariavelObjetivo(chosen){
            var findArray =  this.variaveis.find(obj => { return obj.nome == this.chosen; })
            if(findArray != null && findArray.tipo != 2){
                findArray.tipo = 2;
                console.log(chosen);
                
            }else{
               alert('Nananinanão');
            }
        },
        removeVariavelObjetivo(chosen){
            var findArray =  this.variaveis.find(obj => { return obj.nome == this.chosen; })
            if(findArray != null && findArray.tipo != 1){
                findArray.tipo = 1;
                console.log(chosen);
            }else{
                alert('Nananinanão');
            }
        },
    }
}
</script>