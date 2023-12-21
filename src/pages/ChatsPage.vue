<script setup>
import axios from 'axios'
 import { ref } from 'vue';
const all_users = ref([]);
const username = ref(localStorage.username);
const socket = new WebSocket('ws://127.0.0.1:8000/ws');
const current_recipient = ref('')


socket.onopen = function (e) {
	socket.send(JSON.stringify({
		message: 'Hello from Js client'
	}));
};

socket.onmessage = function (event) {
	try {
		console.log(event);
	} catch (e) {
		console.log('Error:', e.message);
	}
};

function getAllUsers() {
	axios.get(import.meta.env.VITE_BACKEND_URL + '/' + 'chat' + '/' + 'userlist' + '/',
		{
			headers: {
				'Access-Control-Allow-Origin': '*',
				'Content-type': 'application/json'
			}
		}
	).then(function (response) {
	  all_users.value = response.data
	}
	)
}
 getAllUsers()
 const messagelist = ref([])
 function get_message_list() {
       axios.get(import.meta.env.VITE_BACKEND_URL + '/' + 'chat' + '/' + 'messagelist' + '/',
	       { headers: {
		 'Access-Control-Allow-Origin': '*',
		 'Content-type': 'application/json'}
	       }
       ).then(function (response) {
	 messagelist.value = response.data
	 getAllUsers()
	 messagelist.value.forEach((message) => {
	   all_users.value.forEach((user) => {
	     if (user['id'] == message['msg_to'] ) {
	       message['msg_to'] = user['username']
	     }
	     if (user['id'] == message['msg_from'] ) {
	       message['msg_from'] = user['username']
	     }
	   })
	 })
  	      }
       ).catch(function (response) {
	 console.log(response)
  	      }
	)

}
 get_message_list()

 const localStorageData = ref(localStorage)
</script>
<template>
  <div>
    <h5 style="text-align: center; margin:auto">chat page</h5> <br>
    <q-select style="margin: 5% 10%" option-value="username" option-label="username"   filled v-model="current_recipient" :options="all_users" label="recipient" />
    <li id="messagelist" v-for="message in messagelist" :key="message.id"  style="margin: 1% 10%; background-color: #EFEFEF" v-show="message.msg_to==localStorageData.username || message.msg_from==localStorageData.username">
      <div v-if="message.msg_to==localStorageData.username && message.msg_from==current_recipient"> {{ message.content }} </div>
      <div v-if="message.msg_from==localStorageData.username && message.msg_to==current_recipient" style="text-align: right"> {{ message.content }} </div>
    </li>
    <div style="margin: 1% 10%">
      <q-input label="your message here"> </q-input> <q-btn> send </q-btn>
	        &ensp;  you are logged in as: {{ username }}
    </div>
  </div>
</template>
