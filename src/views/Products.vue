<template>
  <div>
    <input type="text" v-model="search" placeholder="search" />
    <button @click="searchProducts">search</button>
    <div v-if="fetching">Loading...</div>
    <div v-else-if="error">Oh no... {{ error }}</div>
    <div v-else>
      <div v-if="data">
        <div v-for="p in data.products" :key="p.id" class="product_card">
          <p>{{ p.title }}</p>
          <p>{{ p.price }}</p>
          <button @click="addFav(p.id)">Добавить</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { useQuery, gql, useMutation } from "@urql/vue";
import { ref } from "@vue/reactivity";
import { makeOperation } from '@urql/core';
import { useRouter, useRoute } from "vue-router";


export default {
  setup() {
    const search = ref(null);
    const router = useRouter();
    const route = useRoute();
    const add = useMutation(
       `
    mutation ($ProductId: Int!, $UserId: String!) {
  create_junction_directus_users_products_item(
    data: { products_id: $ProductId, directus_users_id: $UserId }
  ) {
    id
  }
}
      ` 

      
    );
    
    const result = useQuery({
      query: gql`
        query ($search: String) {
          products(search: $search) {
            id
            title
            price
            spec
          }
        }
      `,
      variables: { search },
    });
    function searchProducts() {
      result.executeQuery();
    }
    


    function move(id) {
      router.push("/products/" + id);
    }

    async function addFav(id) {
      const i = id
      const a = parseInt(i)
      const u = "518c6ae4-9919-4f5c-a494-81a4c8e562a3"
  
      const variables = { ProductId: a, UserId: u }
      add.executeMutation(variables).then((result) => {
        if (result.error) {
          console.error("Oh no!", result.error);
        }
      });
      /* if (!fetching) {
        console.log(info);
        console.log(error);
      }  */
    }

    return {
      search,
      fetching: result.fetching,
      data: result.data,
      error: result.error,
      info: add.data,
      searchProducts,
      move,
      addFav,
    };
  },
};
</script>

<style scoped>
.product_card {
  border: 3px black solid;
  margin: 1rem;
}
</style>
