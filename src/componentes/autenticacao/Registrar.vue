<template>
    <v-container fluid>
        <v-layout>
            <v-flex>
                <v-layout column class="ma-3">
                    <h1 class="headline">Registrar</h1>
                    <v-divider class="mb-3" />
                        <div v-if="erros">
                            <Erros :erros="erros" />
                        </div>
                        <v-text-field label="Nome"
                            v-model="usuario.nome" />
                        <v-text-field label="E-mail"
                            v-model="usuario.email" />
                        <v-text-field label="Senha"
                            v-model="usuario.senha" type="password" />
                        <v-btn color="primary" class="ml-0 mt-3"
                            @click="registrar">
                            Registrar
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
                        <v-text-field label="Perfis" readonly
                            :value="perfis" />
                    </template>
                </v-layout>
            </v-flex>
        </v-layout>
    </v-container>
</template>

<script>
import Erros from '../comum/Erros'
import gql from 'graphql-tag'
import Loading from '../comum/Loading'

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
                this.dados.perfis.map(p => p.rotulo).join(',')
        }
    },
    methods: {

        validarEmail(email) {
            var regex = /^[a-zA-Z0-9.!#$%&'*+/=?^_`{|}~-]+@[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?(?:\.[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?)*$/;
            return regex.test(email);
        },
        registrar() {

            this.dados = null;
            this.loading = true;

            const emailValido = this.validarEmail(this.usuario.email);

            if(!emailValido) {
                const err = new Error("Email Inválido!");
                this.erros = err;
                this.loading = false;
                return;
            }

            this.$api.mutate({
                mutation: gql`
                    mutation (
                        $nome: String!
                        $email: String!
                        $senha: String!
                    ) {
                        registrarUsuario(
                            dados: {
                                nome: $nome
                                email: $email
                                senha: $senha
                            }
                        ) {
                            id nome email perfis { rotulo }
                        }
                    }
                `,
                variables: {
                    nome: this.usuario.nome,
                    email: this.usuario.email,
                    senha: this.usuario.senha,
                }
            }).then(resultado => {
                this.dados = resultado.data.registrarUsuario;
                this.usuario = {};
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
