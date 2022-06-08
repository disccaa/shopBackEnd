<template>
  <div>
    <RouterLink to="/">Home</RouterLink> |
    <RouterLink to="/products">Products</RouterLink>
    <RouterView />
  </div>
</template>

<script>
import { useRouter, useRoute } from 'vue-router'
import { createClient, provideClient} from "@urql/vue";
import router from "./router";


export default {
  setup() {
        const router = useRouter()
    const route = useRoute()
    const client = createClient({

      url: "http://38.242.229.113:8055/graphql/system",
      fetchOptions: () => {
      let auth = 0  
      console.log(1 ,localStorage.getItem('token') )
      const token = auth == 0 ? localStorage.getItem('token') : null
      console.log(route.params.log)

      return {
        headers: { authorization: token ? `Bearer ${token}` : '' }
      };
    },
    });



    provideClient(client);
  },
};
</script>

<style></style>
