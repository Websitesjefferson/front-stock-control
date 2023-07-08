<template>
  <div class="full-width pt-4 pb-4">
    <v-row justify="center" align="center" class="black--text font-weight-bold mt-10">
      <h2>Cadastrar produto</h2>
    </v-row>
    <v-row justify="center" align="center">
      <v-col cols="12" sm="6" md="4">
        <v-form class="pl-10 pr-10 " @submit.prevent="submitForm">
          <v-text-field class="custom-text-field" :class="{'custom-text-color': true}"  v-model="nameProduct" label="Nome do Produto"></v-text-field>
          <v-text-field class="custom-text-field" :class="{'custom-text-color': true}"  v-model="quantityProduct" label="Quantidade do Produto"></v-text-field>
          <v-text-field class="custom-text-field" :class="{'custom-text-color': true}"  v-model="priceProduct" label="Preço do Produto"></v-text-field>

          <v-btn type="submit"  class="custom-button info">Cadastrar</v-btn>
        </v-form>
      </v-col>
    </v-row>
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
.custom-button {
  width: 100%;
  margin-top: 20px;
}
</style>

<script>
  import {URL} from  '../components/Api'
export default {
  data() {
    return {
      nameProduct: '',
      quantityProduct: '',
      priceProduct: '',
      showSnackbar: false,
      snackbarMessage: '',
      snackbarColor: '',
      snackbarTimeout: 3000,
      apiUrl: URL
    };
  },
  methods: {
    submitForm() {
      // Chama a função para enviar os dados do formulário para a API
      this.sendToAPI();
    },
    sendToAPI() {
      // Importação dinâmica do Axios
      import('axios').then(({ default: axios }) => {
        // Dados do formulário
        const formData = {
          nome: this.nameProduct,
          quantidade: this.quantityProduct,
          preco: this.priceProduct
        };

        // Fazendo a solicitação POST para a API
        axios.post(this.apiUrl, formData)
          .then(response => {
            // Manipular a resposta da API, se necessário
            console.log('Resposta da API:', response.data);
            this.clearFields()
            this.showSnackbarNotification('Cadastrado com sucesso', 'success');
            
          })
          .catch(error => {
            // Manipular erros da solicitação, se houver
            console.error('Erro na solicitação:', error);
          });
      }).catch(error => {
        console.error('Erro na importação do Axios:', error);
      });
      
    },
    clearFields() {
      this.nameProduct = '';
      this.quantityProduct = '';
      this.priceProduct = '';
    },
    showSnackbarNotification(message, color) {
      this.snackbarMessage = message;
      this.snackbarColor = color;
      this.showSnackbar = true;
    }
     
  }
}
</script>
