<template>
    <div class="card" :style="{ borderLeftColor: color }">
      <h3>{{ title }}</h3>
      <p>{{ amount }}</p>
    </div>
  </template>
  
  <script setup>
  defineProps({
    title: String,
    amount: String,
    color: {
      type: String,
      default: '#ccc'
    }
  })
  </script>
  