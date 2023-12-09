<script setup>
  import axios from 'axios'
import { ref } from 'vue'

const username = ref('')
const password = ref('')
const register_dialog = ref(false)
const token = ref('')
const registration_answer = ref('')
const user_logged_in = ref('')
const logged_in = ref(localStorage.token)


  function register() {
    axios.post(import.meta.env.VITE_BACKEND_URL + '/' + 'userauth' + '/' + 'register' + '/',
	       {username: username.value, password: password.value},
	       { headers: {
		 'Access-Control-Allow-Origin': '*',
		 'Content-type': 'application/json'}
	       }
	      ).then(function (response) {
		register_dialog.value = true
		registration_answer.value = response
  	      }
		    ).catch(function (response) {
		register_dialog.value = true
		registration_answer.value = response
  	      }
		     )
		    
  }

function login() {
  axios.post(import.meta.env.VITE_BACKEND_URL + '/' + 'userauth' + '/' + 'token' + '/',
	     {username: username.value, password: password.value},
	       { headers: {
		 'Access-Control-Allow-Origin': '*',
		 'Content-type': 'application/json'}
	       }
	  ).then(function (response) {
	    token.value=response.data.access
	    localStorage.token = token.value
	    logged_in.value = true
  	  }
		)
}

function quit(){
  localStorage.removeItem("token")
  logged_in.value = false
}
		      
</script>
  <template>
  <div style="display:flex; flex-direction:column">
  <div class="q-pa-md q-gutter-sm">
    <q-input filled v-model="username" label="username" />
    <q-input filled v-model="password" label="password" />
    <q-btn @click="login" color="white" text-color="black" label="login" />
    <q-btn @click="register" color="white" text-color="black" label="register" />
    <q-btn @click="quit" color="white" text-color="black" label="quit" />
  </div>
  <div v-if="logged_in">
    you are logged in
</div>
  </div>

   <q-dialog v-model="register_dialog">
      <q-card>
        <q-card-section>
          <div class="text-h6">response</div>
        </q-card-section>

        <q-card-section class="q-pt-none">
  {{ registration_answer }}
        </q-card-section>

        <q-card-actions align="right">
          <q-btn flat label="OK" color="primary" v-close-popup />
        </q-card-actions>
      </q-card>
    </q-dialog>

</template>
