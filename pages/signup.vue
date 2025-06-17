<template>
    <div class="auth-container">
      <h1>Sign Up</h1>
      <form @submit.prevent="signUp">
        <input v-model="username" type="text" placeholder="Username" required />
        <input v-model="password" type="password" placeholder="Password" required />
        <button type="submit">Daftar</button>
      </form>
      <p v-if="error" class="error">{{ error }}</p>
      <p>
        Sudah punya akun?
        <router-link to="/login">Login</router-link>
      </p>
    </div>
  </template>
  
  <script setup>
  import { ref } from 'vue'
  import { useRouter } from 'vue-router'
  
  const router = useRouter()
  const username = ref('')
  const password = ref('')
  const error = ref('')
  
  function signUp() {
    const users = JSON.parse(localStorage.getItem('users') || '[]')
    const exists = users.find(u => u.username === username.value)
    if (exists) {
      error.value = 'Username sudah terdaftar.'
      return
    }
  
    users.push({ username: username.value, password: password.value })
    localStorage.setItem('users', JSON.stringify(users))
    localStorage.setItem('isLoggedIn', 'true')
    localStorage.setItem('currentUser', username.value)
    router.push('/login')
    }
  </script>
  