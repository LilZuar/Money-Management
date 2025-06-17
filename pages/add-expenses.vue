<template>
  <div class="section">
    <h2>Tambah Pengeluaran</h2>
    <form @submit.prevent="submitExpense" class="transaction-form">
      <input v-model.number="amount" type="number" placeholder="Jumlah" required />

      <select v-model="category" required>
        <option disabled value="">Pilih Kategori</option>
        <option>Makan</option>
        <option>Transportasi</option>
        <option>Hiburan</option>
        <option>Belanja</option>
        <option>Lainnya</option>
      </select>

      <button type="submit">Simpan</button>
    </form>
  </div>

  <div class="section">
    <h3>Riwayat Pengeluaran</h3>
    <ul class="transaction-list" v-if="expenseList.length > 0">
      <li class="transaction-item" v-for="item in expenseList" :key="item.id">
        <div class="info">
          <span class="category">{{ item.category }}</span>
          <span class="amount expense">-Rp {{ item.amount.toLocaleString() }}</span>
        </div>
        <button class="delete-btn" @click="deleteExpense(item.id)">🗑</button>
      </li>
    </ul>
    <p v-else>Belum ada pengeluaran.</p>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'


const amount = ref(0)
const category = ref('')
const expenseList = ref([])
const currentUser = ref(null)

function loadExpenses() {
  if (!process.client || !currentUser.value) return
  const all = JSON.parse(localStorage.getItem('transactions') || '[]')
  expenseList.value = all.filter(
    item => item.type === 'expense' && item.username === currentUser.value
  )
}

function submitExpense() {
  if (!process.client || !currentUser.value) return

  const newExpense = {
    id: Date.now(),
    amount: amount.value,
    category: category.value,
    type: 'expense',
    createdAt: new Date().toISOString(),
    username: currentUser.value
  }

  const existing = JSON.parse(localStorage.getItem('transactions') || '[]')
  existing.push(newExpense)
  localStorage.setItem('transactions', JSON.stringify(existing))

  amount.value = 0
  category.value = ''
  loadExpenses()
}

function deleteExpense(idToDelete) {
  if (!process.client) return
  const all = JSON.parse(localStorage.getItem('transactions') || '[]')
  const updated = all.filter(item => item.id !== idToDelete)
  localStorage.setItem('transactions', JSON.stringify(updated))
  loadExpenses()
}

onMounted(() => {
  if (process.client) {
    currentUser.value = localStorage.getItem('currentUser')
    loadExpenses()
  }
})
</script>

