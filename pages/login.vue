<template>
    <div class="auth-container">
      <h1>Login</h1>
      <form @submit.prevent="login">
        <input v-model="username" type="text" placeholder="Username" required />
        <input v-model="password" type="password" placeholder="Password" required />
        <button type="submit">Login</button>
      </form>
      <p v-if="error" class="error">{{ error }}</p>
      <p>
        Belum punya akun?
        <router-link to="/signup">Daftar</router-link>
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
  
  function login() {
    const users = JSON.parse(localStorage.getItem('users') || '[]')
    const user = users.find(u => u.username === username.value && u.password === password.value)
  
    if (user) {
      localStorage.setItem('isLoggedIn', 'true')
      localStorage.setItem('currentUser', user.username)
      router.push('/')
    } else {
      error.value = 'Username atau password salah.'
    }
  }
  </script>
  
  