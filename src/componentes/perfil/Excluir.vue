<template>
    <v-container fluid>
        <v-layout>
            <v-flex>
                <v-layout column class="ma-3">
                    <h1 class="headline">Excluir Perfil</h1>
                    <v-divider class="mb-3" />
                    <div v-if="erros">
                        <Erros :erros="erros" />
                    </div>
                    <v-text-field label="ID"
                        v-model.number="filtro.id" />
                    <v-text-field label="Nome"
                        v-model="filtro.nome" />

                    <v-btn color="error" class="ml-0 mt-3"
                        @click="excluirPerfil">
                        Excluir Perfil
                    </v-btn>
                </v-layout>
            </v-flex>
            <v-flex>
                <v-layout column class="ma-3">
                    <h1 class="headline">Resultado</h1>
                    <v-divider />
                    <Loading :loading="loading" />
                    <template v-if="dados">
                        <v-text-field label="ID" readonly
                            v-model="dados.id" />
                        <v-text-field label="Nome" readonly
                            v-model="dados.nome" />
                        <v-text-field label="Rótulo" readonly
                            v-model="dados.rotulo" />
                    </template>
                </v-layout>
            </v-flex>
        </v-layout>
    </v-container>
</template>

<script>
import Erros from '../comum/Erros'
import Loading from '../comum/Loading'
import gql from 'graphql-tag'

export default {
    components: { Erros, Loading },
    data() {
        return {
            filtro: {},
            dados: null,
            loading: null,
            erros: null
        }
    },
    methods: {
        excluirPerfil() {

            this.erros = null;
            this.dados = null;
            this.loading = true;

            this.$api.mutate({
                mutation: gql`
                    mutation(
                        $id: Int
                        $nome: String
                    ){
                        excluirPerfil(
                            filtro: {
                                id: $id
                                nome: $nome
                            }
                        ){
                            id nome rotulo
                        }
                    }
                `,
                variables: {
                    id: this.filtro.id,
                    nome: this.filtro.nome
                }
            }).then(resultado => {
                this.dados = resultado.data.excluirPerfil;
                this.filtro = {};
                this.erros = null;
                this.loading = null;
            }).catch(err => {
                this.loading = null;
                this.erros = err;
            });
        }
    }
}
</script>

<style>

</style>
