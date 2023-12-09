<template>
  <div v-if="token">
    <div v-for="cat in cats" :key="cat.id">
  <q-card style="width: 300px; margin:25px">
  <q-card-section style="display: flex">
  {{cat.name}}
  <q-input label="rename" v-model="rename_models[cat.id]" />
  <q-btn @click="rename(cat.id)"> press to rename </q-btn>
      </q-card-section>
  </q-card>
  </div>
  </div>
  <q-page v-else class="flex flex-center">
  if logged in, refresh the page
    <img
      alt="Quasar logo"
      src="~assets/quasar-logo-vertical.svg"
      style="width: 200px; height: 200px"
    >
  </q-page>
</template>

<script setup>
import {ref, onMounted, computed} from 'vue'
import axios from 'axios'

let rename_models = {}
let token = computed(() => {
return localStorage.token })

function rename(key) {
  console.log(key)
  console.log(rename_models[key])
}

function fetch_cats() {
  axios.get(import.meta.env.VITE_BACKEND_URL + '/' + 'cats' + '/' + 'listcreate' + '/',
	     { headers: {
		 'Access-Control-Allow-Origin': '*',
		 'Content-type': 'application/json',
	       'Authorization' : 'Bearer ' + token.value
	     }
	     } 
	   ).then(function (response) {
	     cats.value = response.data
	     response.data.forEach((element) => {
	       rename_models[element.id] = ""
	     })
  	      }
		    ).catch(function (response) {
		      console.log(response)
  		    }
			   )
}
let cats = ref([])

onMounted(() => {
  fetch_cats()
})

</script>
