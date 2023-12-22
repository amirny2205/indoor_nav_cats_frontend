<script setup>
import axios from 'axios'
import { ref } from 'vue';
const all_users = ref([]);
const username = ref(localStorage.username);
const socket = new WebSocket('ws://' + import.meta.env.VITE_BACKEND_URL + '/ws');
const current_recipient = ref('')
const messagelist = ref([])
const localStorageData = ref(localStorage)
const new_message = ref('')

 
socket.onopen = function (e) {
	socket.send(JSON.stringify({
		message: 'Hello from Js client'
	}));
};

socket.onmessage = function (event) {
	try {
		console.log('onmessage')
		get_message_list()
	} catch (e) {
		console.log('Error:', e.message);
	}
};

function getAllUsers() {
	axios.get('http://' + import.meta.env.VITE_BACKEND_URL + '/' + 'chat' + '/' + 'userlist' + '/',
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

function get_message_list() {
	axios.get('http://' + import.meta.env.VITE_BACKEND_URL + '/' + 'chat' + '/' + 'messagelist' + '/',
		{
			headers: {
				'Access-Control-Allow-Origin': '*',
				'Content-type': 'application/json'
			}
		}
	).then(function (response) {
		messagelist.value = response.data
		getAllUsers()
		messagelist.value.forEach((message) => {
			all_users.value.forEach((user) => {
				if (user['id'] == message['msg_to']) {
					message['msg_to'] = user['username']
				}
				if (user['id'] == message['msg_from']) {
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


function new_message_send() {
	socket.send(JSON.stringify({
		msg_from: localStorage.username,
		msg_to: current_recipient.value.username,
		content: new_message.value
	}))
	get_message_list()

}

setInterval(get_message_list, 1000)
</script>
<template>
  <div>
    <h5 style="text-align: center; margin:auto">chat page</h5> <br>
    <q-select style="margin: 5% 10%" option-value="username" option-label="username"   filled v-model="current_recipient" :options="all_users" label="recipient" />
    <li id="messagelist" v-for="message in messagelist" :key="message.id"  style="margin: 1% 10%; background-color: #EFEFEF" v-show="message.msg_to==localStorageData.username && message.msg_from==current_recipient.username || message.msg_from==localStorageData.username && message.msg_to==current_recipient.username">
      <div v-if="message.msg_to==localStorageData.username && message.msg_from==current_recipient.username"> {{ message.content }} </div>
      <div v-if="message.msg_from==localStorageData.username && message.msg_to==current_recipient.username" style="text-align: right"> {{ message.content }} </div>

    </li>
    <div style="margin: 1% 10%">
      <q-input v-model="new_message" label="your message here"> </q-input> <q-btn @click="new_message_send()"> send </q-btn>
      &ensp;  you are logged in as <strong> {{ username }} </strong>
    </div>
  </div>
</template>
