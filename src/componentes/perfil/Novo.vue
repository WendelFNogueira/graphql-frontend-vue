<template>
    <v-container fluid>
        <v-layout>
            <v-flex>
                <v-layout column class="ma-3">
                    <h1 class="headline">Novo Perfil</h1>
                    <v-divider class="mb-3" />
                        <div v-if="erros">
                            <Erros :erros="erros" />
                        </div>
                        <v-text-field label="Nome"
                            v-model="perfil.nome" />
                        <v-text-field label="Rótulo"
                            v-model="perfil.rotulo" />
                        <v-btn color="primary" class="ml-0 mt-3"
                            @click="novoPerfil">
                            Novo Perfil
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
            perfil: {},
            dados: null,
            loading: null,
            erros: null
        }
    },
    methods: {
        novoPerfil() {

            this.erros = null;
            this.dados = null;
            this.loading = true;

            this.$api.mutate({
                mutation: gql`
                    mutation(
                        $nome: String
                        $rotulo: String
                    ){
                        novoPerfil(
                            dados: {
                                nome: $nome
                                rotulo: $rotulo
                            }
                        ){
                            id nome rotulo
                        }
                    }
                `,
                variables: {
                    nome: this.perfil.nome,
                    rotulo: this.perfil.rotulo
                }
            }).then(resultado => {
                this.dados = resultado.data.novoPerfil;
                this.perfil = {};
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
