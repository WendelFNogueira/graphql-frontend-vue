<template>
    <v-container fluid>
        <v-layout>
            <v-flex>
                <v-layout column class="ma-3">
                    <h1 class="headline">Login</h1>
                    <v-divider class="mb-3" />
                        <div v-if="erros">
                            <Erros :erros="erros" />
                        </div>
                        <v-text-field label="E-mail"
                            v-model="usuario.email" />
                        <v-text-field label="Senha"
                            v-model="usuario.senha" type="password" />
                        <v-btn color="primary" class="ml-0 mt-3"
                            @click="login">
                            Logar
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
                        <v-text-field label="E-mail" readonly
                            v-model="dados.email" />
                        <v-text-field label="Token" readonly
                            v-model="dados.token" />
                    </template>
                </v-layout>
            </v-flex>
        </v-layout>
    </v-container>
</template>

<script>
import { mapActions } from 'vuex'
import Erros from '../comum/Erros'
import Loading from '../comum/Loading'
import gql from 'graphql-tag'

export default {
    components: { Erros, Loading },
    data() {
        return {
            usuario: {},
            dados: null,
            loading: null,
            erros: null
        }
    },
    computed: {
        perfis() {
            return this.dados && this.dados.perfis &&
                this.dados.perfis.map(p => p.nome).join(',')
        }
    },
    methods: {
        ...mapActions(['setUsuario']),
        login() {

            this.dados = null;
            this.loading = true;

            this.$api.query({
                query: gql`
                    query(
                        $email: String!
                        $senha: String!
                    ) {
                        login(
                            dados: {
                                email: $email
                                senha: $senha
                            }
                        ) {
                            id, nome, email, perfis { rotulo }, token
                        }
                    }
                `,
                variables: {
                    email: this.usuario.email,
                    senha: this.usuario.senha
                }
            }).then(resultado => {
                this.dados = resultado.data.login;
                this.usuario = {};
                this.erros = null;
                this.loading = null;
                this.setUsuario(this.dados);
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
