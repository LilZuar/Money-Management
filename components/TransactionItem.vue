<template>
    <div class="transaction">
      <span>{{ label }}</span>
      <span>{{ amount }}</span>
    </div>
  </template>
  
  <script setup>
  defineProps({
    label: String,
    amount: String
  })
  </script>
  