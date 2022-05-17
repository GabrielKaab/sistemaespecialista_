<template>
    <div>
        <input type="text" placeholder="Nome da variável" v-model="nome_variavel"/>
        <input type="text" placeholder="Valor" v-model="valor_variavel"/>
        <br>
        <btn class="btn btn-info" @click="criarVariavel(nome_variavel, valor_variavel)">Add</btn>
        {{variavel}}
    </div>
</template>
<script>

export default {
    name: 'Variaveis',
    data() {
        return {
            variavel: []
        }
    },
    methods: {
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
        }
    }
}
</script>