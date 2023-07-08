<template>
 

  <div> 
    <v-row justify="center" align="center" class="mt-5">
    <v-text-field class="col-sm-6 custom-text-field" :class="{'custom-background': true, 'custom-text-color': true}" v-model="searchTerm" label="Buscar produto"></v-text-field>
    </v-row>
     <template v-if="filteredItems.length === 0">
      <div justify="center" align="center">
        <v-row justify="center" align="center" class="mb-5 mt-5 black--text">
          Nenhum Produto cadastrado
        </v-row>
        <div>
          <router-link to="/" class="black--text font-weight-bold">Cadastrar produto</router-link>
        </div>
      </div>
    
    </template>
    <v-row justify="center" align="center">
      <v-col v-for="item in filteredItems" :key="item.id" cols="12" sm="6" md="7">
        <v-card class="item-card">
          <v-card-text class="item-row text-center"
            style="background-color: #cccccc; display: flex; align-items: center;">
            <div>
              <label class="subtitle-1 xs12 sm6 md4 lg3 font-weight-bold black--text">Nome do produto</label>
              <div class="subtitle-1 xs12 sm6 md4 lg3 black--text">{{ item.nome }}</div>
            </div>
            <div>
              <label class="subtitle-1 xs12 sm6 md4 lg3 font-weight-bold black--text">Quantidade</label>
              <div class="subtitle-1 xs12 sm6 md4 lg3 black--text">{{ item.quantidade }}</div>
            </div>
            <div>
              <label class="subtitle-1 xs12 sm6 md4 lg3 font-weight-bold black--text">Preço por unidade</label>
              <div class="subtitle-1 xs12 sm6 md4 lg3 black--text">R$ {{ item.preco.toLocaleString('pt-BR', {
                minimumFractionDigits: 2
              }) }}</div>
            </div>

            <div class="icons">
              <v-icon size="25" color="black" class="mr-2" @click="openModalEdit(item)">mdi-pencil</v-icon>
              <v-icon size="25" color="black"  @click="deleteItem(item)">mdi-delete</v-icon>
            </div>
          </v-card-text>
        </v-card>
      </v-col>

    </v-row>

    <!-- Modal de edição -->
    <v-dialog v-model="modalEditVisible" max-width="500px">
      <v-card style="background-color: #999999;">
        <v-card-title>
          <span class="headline">Editar Produto</span>
        </v-card-title>
        <v-card-text>
          <v-text-field v-model="itemEdit.nome" outlined  label="Nome do Produto"></v-text-field>
          <v-text-field v-model="itemEdit.quantidade" outlined  label="Quantidade do Produto"></v-text-field>
          <v-text-field v-model="itemEdit.preco" outlined  label="Preço do Produto"></v-text-field>
        </v-card-text>
        <v-card-actions>
          <v-btn class="info"  @click="saveEdit">Salvar</v-btn>
          <v-btn class="info"  @click="closeModalEdit">Cancelar</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <v-snackbar v-model="showSnackbar" :timeout="snackbarTimeout" :color="snackbarColor">
      {{ snackbarMessage }}
    </v-snackbar>
  </div>
</template>

<style>

.custom-text-field .v-label {
  color: rgba(0, 0,0, 0.3);
 
}

.custom-text-color input {
  color: black !important;
  
}
.item-card {
  margin-top: 15px;
}

.item-row {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
}

.icons {
  display: flex;
  align-items: center;
}
</style>

<script>
import {URL} from  '../components/Api'
export default {
  name: 'InspirePage',
  data() {
    return {
      items: [],
      showSnackbar: false,
      snackbarMessage: '',
      snackbarColor: '',
      snackbarTimeout: 3000,
      modalEditVisible: false,
      itemEdit: {},
      searchTerm: '',
      apiUrl: URL
    };
  },
  mounted() {
    this.fetchItems();
  },
  computed: {
    filteredItems() {
      if (this.searchTerm === '') {
      return this.items.filter(item => !item.excluido);
    } else {
      const searchTermLowerCase = this.searchTerm.toLowerCase();
      return this.items.filter(item => {
        const nomeLowerCase = item.nome.toLowerCase();
        return (
          !item.excluido &&
          nomeLowerCase.includes(searchTermLowerCase)
        );
      });
    }
  }
    },
  
  methods: {
    // Lógica para buscar itens
    fetchItems() {
      import('axios')
        .then(({ default: axios }) => {
          axios.get(this.apiUrl)
            .then(response => {
              this.items = response.data;
              console.log(response.data);
            })
            .catch(error => {
              console.error(error);
            });
        })
        .catch(error => {
          console.error(error);
        });
    },
    openModalEdit(item) {
      this.itemEdit = { ...item };
      this.modalEditVisible = true;
    },
    saveEdit() {
      // Lógica para salvar as alterações do item editado
      import('axios')
        .then(({ default: axios }) => {
          axios.put(`${this.apiUrl}/${this.itemEdit.id}`, this.itemEdit)
            .then(response => {
              console.log(`Item com ID ${this.itemEdit.id} editado com sucesso`);
              this.fetchItems();
              this.closeModalEdit();
              this.showSnackbarNotification('Item editado com sucesso!', 'success');
            })
            .catch(error => {
              console.error(error);
              this.showSnackbarNotification('Erro ao editar o item', 'error');
            });
        })
        .catch(error => {
          console.error(error);
        });
    },

    closeModalEdit() {
      this.modalEditVisible = false;
      this.itemEdit = {};
    },
    // Lógica para Deletar item
    deleteItem(item) {
      import('axios')
        .then(({ default: axios }) => {
          axios.delete(`${this.apiUrl}/${item.id}`)
            .then(response => {
              console.log(`Item com ID ${item.id} excluído com sucesso`);
              this.fetchItems();
              this.showSnackbarNotification('Item excluído com sucesso', 'success');
            })
            .catch(error => {
              console.error(error);
              this.showSnackbarNotification('Erro ao excluir o item', 'error');
            });
        })
        .catch(error => {
          console.error(error);
        });
    },

    showSnackbarNotification(message, color) {
      this.snackbarMessage = message;
      this.snackbarColor = color;
      this.showSnackbar = true;
    }

  }
}
</script>
