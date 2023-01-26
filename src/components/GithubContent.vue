<template>
    <div>
      <v-row>

        <v-col cols="9" class="vcol" offset="2" >
            <h2 v-if="repo">{{ repo.name}}/{{contentAtual }}</h2>
            <v-simple-table>
              <template v-slot:default>


                    <thead>
                       <tr>
                          <th class="vpasta">Arquivo/Pasta</th>
                        </tr>
                    </thead>


                <tbody>
                  <tr v-for="content in contents" :key="content.number">

                    <td>
                      <v-icon>{{files[getIcon(content.name)]}}</v-icon>
                    {{ content.name }}</td>
                    <td class="text-left button" v-if="content.type == 'dir'"><v-btn @click="abreDir(content)">abra</v-btn></td>
                </tr>
                </tbody>
              </template>
          </v-simple-table>
        </v-col>
      </v-row>
      <v-row>
        <v-col cols="12">
          <v-progress-circular indeterminate color="primary" v-if="loading"></v-progress-circular>
          <v-btn color="primary" v-if="temmais" @click="listaContent">MAIS</v-btn>
        </v-col>
      </v-row>
      <v-btn v-if="novoContent.length > 0" class="mb-5" @click="voltarDir">
        Voltar
      </v-btn>
    </div>
  </template>

  <script>

    import {api} from '~api'

    export default {
      props: ['repo'],
      data: () => ({
        contents: [],
        loading: false,
        temmais: false,
        currentPage: 1,
        novoContent: [],
        contentAtual: '',
       files: {
        html: 'mdi-language-html5',
        js: 'mdi-nodejs',
        json: 'mdi-code-json',
        md: 'mdi-language-markdown',
        pdf: 'mdi-file-pdf',
        png: 'mdi-file-image',
        svg : 'mdi-file-document-outline',
        xls: 'mdi-file-excel',
        yml: 'mdi-file',
        py : "mdi-language-python",
        ico: 'mdi-file',
        txt: 'mdi-clipboard-text',
        css: 'mdi-language-css3',
        gitignore: 'mdi-block-helper',
        vscode: 'mdi-laptop'
      },
      }),
      methods: {
        async listaContent(){
          this.loading = true
          this.contents = await api.listaContent(this.repo.owner.login, this.repo.name)
          this.loading = false
        },
        async abreDir(content){
            let caminho = content.path
            this.loading = true
            this.contents = await api.abreDir(this.repo.owner.login, this.repo.name, caminho)
            this.novoContent.push(caminho)
            this.contentAtual = caminho
            this.loading = false
        },
        async voltarDir() {
            this.loading = true;
            this.novoContent.pop();
            let antigoContent =
                this.novoContent.length == 1 ? this.novoContent[0] : this.novoContent[-1];
            if (antigoContent == undefined)
                antigoContent = ''
            this.contents = await api.abreDir(
              this.repo.owner.login,
              this.repo.name,
              antigoContent
            );
            this.contentAtual = antigoContent
            this.loading = false;
        },
        getIcon(str){
          console.log(str)
          if (str.includes('.')) {
          let i = str.split('.').pop()
          return i
          }
        }
      },
      watch: {
        repo(){
            this.novoContent = []
            this.listaContent()
          }
        }
      }
  </script>
  <style>
.vcol{
  border-color:grey !important;
  margin-top: 20px;
}
.vcard{
  background-color: goldenrod;
}
.button{
  border-bottom: none !important;
}
.vpasta{
  text-align: left;
  background-color: white;
}
  </style>