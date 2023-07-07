<template>
  <v-app  style="background-color: #cccccc">
    <v-app-bar app color="#cccccc" dark>
    <v-row class="black--text font-weight-bold ml-3">
      <h2>Controle de estoque</h2>
    </v-row>

    <v-menu offset-y v-if="$vuetify.breakpoint.smAndDown">
      <template v-slot:activator="{ on, attrs }">
        <v-btn text v-bind="attrs" v-on="on">
          <v-icon>mdi-menu</v-icon>
        </v-btn>
      </template>
      <v-list style="background-color: #999999;">
        <v-list-item to="/" class="black--text font-weight-bold no-text-decoration">
          <v-list-item-icon>
           <v-list-item-title>Cadastrar produto</v-list-item-title>
          </v-list-item-icon>
          
        </v-list-item>
        <v-list-item to="/registeredProduct" class="black--text font-weight-bold no-text-decoration">
          <v-list-item-icon>
            <v-list-item-title>Produtos Cadastrados</v-list-item-title>
          </v-list-item-icon>
          
        </v-list-item>
      </v-list>
    </v-menu>

    <div v-else>
      <router-link to="/" class="black--text font-weight-bold no-text-decoration">Cadastrar produto</router-link>
      <router-link to="/registeredProduct" class="black--text font-weight-bold mr-10 ml-10 no-text-decoration">Produtos Cadastrados</router-link>
    </div>
  </v-app-bar>
    <v-main>
      <router-view></router-view>
    </v-main>
  </v-app>
</template>
<style>
.no-text-decoration {
  text-decoration: none;
  padding: 10px;
  transition: all 300ms;

}
.no-text-decoration:hover{
  opacity: 0.4;
}

</style>

<script>
export default {
  name: 'DefaultLayout',
  data() {
    return {
      clipped: false,
      drawer: false,
      fixed: false,
      items: [
        {
          icon: 'mdi-apps',
          title: 'Welcome',
          to: '/'
        },
        {
          icon: 'mdi-chart-bubble',
          title: 'registeredProduct',
          to: '/registeredProduct'
        }
      ],
      miniVariant: false,
      right: true,
      rightDrawer: false,
      title: 'Vuetify.js'
    };
  }
};
</script>
