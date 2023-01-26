<template>
  <div>
    <div class="text-center mt-15">
      <p v-if="this.image">
          <v-avatar  size="128">
          <img
            :src="this.image"
          >
        </v-avatar>
      </p>
    </div>
    <v-row class="text-center mt-15">

      <v-col cols="6">
        <v-autocomplete
        v-model="user"
        label="Encontre seu usuário"
        :items="userlist"
        :loading="userloading"
        :search-input.sync="usersearch"
        item-text="login"
      />
      </v-col>
      <v-col cols="6">
        <v-select
          v-model="repo"
          :items="repolist"
          :loading="repoloading"
          item-text="name"
          label="Selecione o repositorio"
          return-object
          single-line
        ></v-select>
      </v-col>
    </v-row>
  </div>
</template>

<script>

  import {debouncerdecorator} from '@/helpers/debouncer.js'
  import {api} from '~api'

  export default {
    data: () => ({
      image:"",
      user: null,
      repo: null,
      usersearch: null,
      userlist: [],
      repolist: [],
      userloading: false,
      repoloading: false,
    }),
    methods: {
      procuraUsuariosGithub: debouncerdecorator(async function () { // atenção: não use ()=>{} aqui. vai quebrar o decorator
        this.userloading = true
        const data = await api.search_users(this.usersearch)
        this.userlist = data.items
        this.userloading = false
      }, 500),
      async listaRepositorios(){
        this.repoloading = true
        const data = await api.lista_repos(this.user)
        this.repolist = data
        this.repoloading = false
      },
      async getImage(user){
        const data = await api.search_users(user)
        this.image = await data.items[0].avatar_url
      }

    },
    watch: {
      usersearch () {
        this.procuraUsuariosGithub()
      },
      user() {
        if(this.user){
          this.listaRepositorios()
          this.getImage(this.user)
        }
      },
      repo () {
        this.$emit('reposelected', this.repo)
      }
    }
  }
</script>
