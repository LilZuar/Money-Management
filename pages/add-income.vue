<template>
    <div class="section">
      <h2>Tambah Pemasukan</h2>
      <form @submit.prevent="submitIncome" class="transaction-form">
        <input v-model.number="amount" type="number" placeholder="Jumlah" required />
        <select v-model="category" required>
          <option disabled value="">Pilih Kategori</option>
          <option>Gaji</option>
          <option>Bonus</option>
          <option>Freelance</option>
          <option>Investasi</option>
          <option>Lainnya</option>
        </select>
        <button type="submit">Simpan</button>
      </form>
    </div>
  
    <div class="section">
      <h3>Riwayat Pemasukan</h3>
      <ul class="transaction-list" v-if="incomeList.length > 0">
        <li class="transaction-item" v-for="item in incomeList" :key="item.id">
          <div class="info">
            <span class="category">{{ item.category }}</span>
            <span class="amount income">+Rp {{ item.amount.toLocaleString() }}</span>
          </div>
          <button class="delete-btn" @click="deleteIncome(item.id)">🗑</button>
        </li>
      </ul>
      <p v-else>Belum ada pemasukan.</p>
    </div>
  </template>
  
  <script setup>
  import { ref, onMounted } from 'vue'
  
  const amount = ref(0)
  const category = ref('')
  const incomeList = ref([])
  const currentUser = ref(null)
  
  onMounted(() => {
    if (process.client) {
      currentUser.value = localStorage.getItem('currentUser')
      loadIncomes()
    }
  })

  const formattedAmount = computed({
  get() {
    return rawAmount.value.toLocaleString('id-ID')
  },
  set(value) {
    const numberOnly = value.replace(/\D/g, '')
    rawAmount.value = parseInt(numberOnly) || 0
  }
})
  
  function loadIncomes() {
    if (!process.client || !currentUser.value) return
    const all = JSON.parse(localStorage.getItem('transactions') || '[]')
    incomeList.value = all.filter(
      item => item.type === 'income' && item.username === currentUser.value
    )
  }
  
  function submitIncome() {
    if (!process.client) return
  
    const newIncome = {
      id: Date.now(),
      amount: amount.value,
      category: category.value,
      type: 'income',
      createdAt: new Date().toISOString(),
      username: currentUser.value
    }
  
    const existing = JSON.parse(localStorage.getItem('transactions') || '[]')
    existing.push(newIncome)
    localStorage.setItem('transactions', JSON.stringify(existing))
  
    amount.value = 0
    category.value = ''
    loadIncomes()
  }
  
  function deleteIncome(idToDelete) {
    if (!process.client) return
    const all = JSON.parse(localStorage.getItem('transactions') || '[]')
    const updated = all.filter(item => item.id !== idToDelete)
    localStorage.setItem('transactions', JSON.stringify(updated))
    loadIncomes()
  }
  </script>
  
  