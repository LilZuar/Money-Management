<script setup>
import { onMounted, ref, computed } from 'vue'
import { useRouter } from 'vue-router'
import Card from '~/components/Card.vue'
import TransactionItem from '~/components/TransactionItem.vue'
import Chart from '~/components/Chart.vue'

const router = useRouter()
const transactions = ref([])
const user = ref(null)

onMounted(() => {
  // Cek login
  const isLoggedIn = localStorage.getItem('isLoggedIn')
  const currentUser = localStorage.getItem('currentUser')

  if (!isLoggedIn || !currentUser) {
    router.push('/login')
    return
  }

  // Ambil data user dari localStorage
  const users = JSON.parse(localStorage.getItem('users') || '[]')
  user.value = users.find(u => u.username === currentUser)

  // Ambil transaksi
  const savedTransactions = localStorage.getItem('transactions')
  if (savedTransactions) {
    const allTransactions = JSON.parse(savedTransactions)
    transactions.value = allTransactions.filter(tx => tx.username === currentUser)
  }
})

// Waktu sekarang
const now = new Date()
const currentMonth = now.getMonth()
const currentYear = now.getFullYear()

const currentMonthTransactions = computed(() =>
  transactions.value.filter(tx => {
    const date = new Date(tx.createdAt)
    return (
      date.getMonth() === currentMonth &&
      date.getFullYear() === currentYear
    )
  })
)

const totalIncome = computed(() =>
  currentMonthTransactions.value
    .filter(tx => tx.type === 'income')
    .reduce((sum, tx) => sum + tx.amount, 0)
)

const totalExpenses = computed(() =>
  currentMonthTransactions.value
    .filter(tx => tx.type === 'expense')
    .reduce((sum, tx) => sum + tx.amount, 0)
)

const totalBalance = computed(() => totalIncome.value - totalExpenses.value)
</script>

<template>
  <div>
    <h1>Halo, {{ user?.name || user?.username || 'Pengguna' }} 👋</h1>

    <div class="card-container">
      <Card title="Total Balance" :amount="'Rp ' + totalBalance.toLocaleString()" color="#3498db" />
      <Card title="Income" :amount="'Rp ' + totalIncome.toLocaleString()" color="#2ecc71" />
      <Card title="Expenses" :amount="'Rp ' + totalExpenses.toLocaleString()" color="#e74c3c" />
    </div>

    <Chart />

    <div class="card">
      <h2>Recent Transactions</h2>
      <TransactionItem
        v-for="(tx, index) in transactions.slice().reverse().slice(0, 5)"
        :key="index"
        :label="tx.category"
        :amount="(tx.type === 'expense' ? '-' : '+') + 'Rp ' + tx.amount"
      />
    </div>
  </div>
</template>
