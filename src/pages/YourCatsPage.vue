<template>
  <div v-if="token">
    <q-card style="width: 300px; margin:25px">
      <q-card-section style="display: flex">
	  <q-input label="name" v-model="new_cat_name" />
	  <q-btn @click="create_cat()"> CREATE ENTRY </q-btn>
	</q-card-section>
    </q-card>
    <hr>
    <div v-for="cat in cats" :key="cat.id">
      <q-card style="width: 400px; margin:25px">
	<q-card-section style="display: flex">
	  {{cat.name}}
	  <q-input label="rename" v-model="rename_models[cat.id]" />
	  <q-btn @click="rename(cat.id)"> press to rename </q-btn>
	  <q-btn @click="dlt(cat.id)"> press to delete </q-btn>
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
let new_cat_name = ref('')
function create_cat(){
	axios.post('http://' + import.meta.env.VITE_BACKEND_URL + '/' + 'cats' + '/' + 'listcreate' + '/',
		{ name: new_cat_name.value },
		{
			headers: {
				'Access-Control-Allow-Origin': '*',
				'Content-type': 'application/json',
				'Authorization': 'Bearer ' + token.value
			}
	     }).then((response) => {
	       fetch_cats()
	     }).catch((err) => {
	       console.log(err)
	     }
		     )
}

function dlt(key) {
	axios.delete('http://' + import.meta.env.VITE_BACKEND_URL + '/' + 'cats' + '/' + 'detail' + '/' + key + '/',
		{
			headers: {
				'Access-Control-Allow-Origin': '*',
				'Content-type': 'application/json',
			}
	    } 
	     ).then(function (response) {
	       fetch_cats()
	     }).catch(function (response) {
		   console.log(response)
  		 }
		     )
}


function rename(key) {
	axios.patch('http://' + import.meta.env.VITE_BACKEND_URL + '/' + 'cats' + '/' + 'detail' + '/' + key + '/',
		{ name: rename_models[key] },
		{
			headers: {
				'Access-Control-Allow-Origin': '*',
				'Content-type': 'application/json',
			}
	    } 
	     ).then(function (response) {
	       fetch_cats()
	     }).catch(function (response) {
		   console.log(response)
  		 }
		     )
}

function fetch_cats() {
	axios.get('http://' + import.meta.env.VITE_BACKEND_URL + '/' + 'cats' + '/' + 'listcreate' + '/',
		{
			headers: {
				'Access-Control-Allow-Origin': '*',
				'Content-type': 'application/json',
				'Authorization': 'Bearer ' + token.value
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
