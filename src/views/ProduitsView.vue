<template>
  <main>
    <div>
      <table>
        <caption>Liste des produits Page {{data.page +1}}</caption>
        <tr>
          <th>Nom</th>
          <th>Prix</th>
          <th>Stock</th>
          <th>Commandés</th>
        </tr>
        <!-- Si le tableau des catégories est vide -->
        <tr v-if="!data.listeProduits">
          <td colspan="4">Veuillez patienter, chargement des produits...</td>
        </tr>
        <!-- Si le tableau des catégories n'est pas vide -->
        <tr v-for="produits in data.listeProduits" :key="produits.code">
          <td>{{ produits.nom }}</td>
          <td>{{ produits.prixUnitaire }}</td>
          <td>{{ produits.unitesEnStock }}</td>
          <td>{{ produits.unitesCommandees }}</td>
        </tr>
      </table>
      <button @click="data.page = 0;chargeProduits()">First page</button>
      <button @click="data.page = data.page-1;chargeProduits()" :disabled="data.page === 0">Previous page</button>
      <button @click="data.page = data.page+1;chargeProduits()" :disabled="data.page === data.totalPages">Next Page</button>
      <button @click="data.page = data.totalPages;chargeProduits()">Last page</button>
    </div>
  </main>
</template>

<script setup>
import {BACKEND, doAjaxRequest} from "../api";
import {onMounted, reactive} from "vue";



let data = reactive({
  // La liste des catégories affichée sous forme de table
  listeProduits: [],
  totalPages:0,
  page:0
});
function chargeProduits() {
  // Appel à l'API pour avoir la liste des catégories
  // Trié par code, descendant
  // Verbe HTTP GET par défaut
  doAjaxRequest("https://springajax.herokuapp.com/api/produits?page=" + data.page + "&size=5")
      .then((json) => {
        data.listeProduits = json._embedded.produits;
        data.totalPages = json.page.totalPages-1;
      })
      .catch((error) => alert(error.message));
}

onMounted(chargeProduits);

</script>

<style scoped>

table{
  width: 100%;
}

th {
  padding-top: 10px;
  padding-bottom: 10px;
  text-align: left;
  background-color: #282828;
  color: #dddddd;
}

td,
th {
  border: 1px solid #222222;
  padding: 10px;
}

</style>