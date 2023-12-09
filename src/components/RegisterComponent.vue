<script setup>
  import axios from 'axios'

  function register(username, password) {
    axios.post(import.meta.env.VITE_BACKEND_URL + '/' + 'userauth' + '/' + 'register' + '/',
	       {username: 'username', password: 'password'},
	       { headers: {
		 'Access-Control-Allow-Origin': '*',
		 'Content-type': 'application/json'}
	       }
	      ).then(function (response) {
		 console.log(response)
	       }
		       )
  }
		      
</script>
<template>
  <div class="q-pa-md q-gutter-sm">
    <q-input filled v-model="username" label="username" />
    <q-input filled v-model="password" label="password" />
    <q-btn color="white" text-color="black" label="login" />
    <q-btn @click="register" color="white" text-color="black" label="register" />
    <q-btn color="white" text-color="black" label="quit" />
  </div>
</template>
