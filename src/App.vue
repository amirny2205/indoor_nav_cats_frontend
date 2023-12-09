<template>
  <router-view />
</template>

<script>
import { defineComponent } from 'vue'
import axios from 'axios'

const client = axios.create({
    baseURL: process.env.BACKEND_URL
});

export default defineComponent({
  name: 'App'
})
</script>
