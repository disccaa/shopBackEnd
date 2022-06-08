<template>
<div>
    <div>
      <input type="text" name="first" placeholder="Write your name">
  <input type="text" name="last"  placeholder="Write your last name">
  <input type="email" name="email" placeholder="Write your email">
  <input type="password" name="password" placeholder="Write your password">
  <button @click="createUser">Завершить регистрацию</button>   
    </div>
  <div>
    
    <input type="email" name="auth_email" placeholder="Write your email">
    <input type="password" name="auth_password" placeholder="Write your password"> 
    <button @click="getAuth">Войти</button>
    <button @click="del">выйти</button>
  </div>
</div>
 



 
</template>

<script>
import { useMutation } from "@urql/vue";
import { ref } from '@vue/reactivity';
import { useRouter, useRoute } from 'vue-router'

export default {
  setup() {
    const router = useRouter()
    const route = useRoute()
    const access = useMutation(
      `   
mutation ($email: String!, $password: String!) {
  auth_login(email: $email, password: $password, mode: json) {
    access_token
    expires
    refresh_token
  }
}
     `
    );
    const createCustomer = useMutation(
      `
     mutation ($firstName: String, $lastName: String, $email: String, $password: String){
  create_users_item(data: {
    first_name: $firstName,
    last_name: $lastName,
    email: $email,
    password: $password,
  	status: "active",
    provider: "default",
    role: "cf49ed74-e9df-451d-ae3a-9a77c07fcf7a" 
  })
}
     `
    );
     function getAuth() {
      const password = document.querySelector("input[name=auth_password]").value
      const email = document.querySelector("input[name=auth_email]").value
      const variables = { email: email, password: password }
      access.executeMutation(variables).then((result) => {
        localStorage.setItem("token", result.data.auth_login.access_token);
      });
      if( localStorage.getItem('token') != null){
        let log = '?log=true'
      router.push(log)
      cons
      } else {
        let log = '?log=false'
        router.push(log)
        console.log('err')
      }

    }
    function createUser() {
        const first = document.querySelector("input[name=first]").value
        const last = document.querySelector("input[name=last]").value
        const email = document.querySelector("input[name=email]").value
        const password = document.querySelector("input[name=password]").value
        const variables = { firstName: first, lastName: last, email: email, password: password }
        createCustomer.executeMutation(variables)   
    }
    function del() {
      let log = '?log=false'
      window.localStorage.clear()
       router.push(log)
    }
    return {
      getAuth,
      createUser,
      del
    };
  },
};
</script>

<style></style>

cf49ed74-e9df-451d-ae3a-9a77c07fcf7a
