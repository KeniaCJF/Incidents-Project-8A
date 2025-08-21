<template>
  <div>
    <router-view />
  </div>
</template>

<script setup>
import { onMounted, ref } from 'vue'
import axios from './api/axios'

const token = ref(null)
const user = ref(null)

onMounted(async () => {

  token.value = localStorage.getItem('auth_token')
  if (token.value) {
    await getUser()
  }
})


const getUser = async () => {
  try {
    if (!token.value) return console.log('No hay token disponible')

    const response = await axios.get('/api/user', {
      headers: {
        Authorization: `Bearer ${token.value}`,
        Accept: 'application/json'
      }
    })

    user.value = response.data
    console.log('Usuario autenticado:', user.value)
  } catch (error) {
    if (error.response?.status === 401) {
      console.log('Usuario no autenticado')
      localStorage.removeItem('auth_token')
    } else {
      console.error('Error al obtener usuario:', error.response)
    }
  }
}
</script>
