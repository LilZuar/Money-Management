<template>
    <div class="sidebar">
      <h2>Menu</h2>
      <NuxtLink to="/" class="nav-link">Dashboard</NuxtLink>
      <NuxtLink to="/add-income" class="nav-link">Add Income</NuxtLink>
      <NuxtLink to="/add-expenses" class="nav-link">Add Expenses</NuxtLink>
      <button class="logout-btn" @click="logout">Logout</button>
    </div>
  </template>
  
  <script setup>
  import { useRouter } from 'vue-router'
  
  const router = useRouter()
  
  function logout() {
    localStorage.removeItem('isLoggedIn')
    localStorage.removeItem('currentUser')
    router.push('/login')
  }
  </script>
  