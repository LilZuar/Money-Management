<template>
    <div class="chart-container">
      <Line :data="chartData" :options="chartOptions" />
    </div>
  </template>
  
  <script setup>
  import { ref, onMounted } from 'vue'
  import { Line } from 'vue-chartjs'
  import {
    Chart as ChartJS,
    Title,
    Tooltip,
    Legend,
    LineElement,
    PointElement,
    CategoryScale,
    LinearScale
  } from 'chart.js'
  
  ChartJS.register(Title, Tooltip, Legend, LineElement, PointElement, CategoryScale, LinearScale)
  
  const chartData = ref({
    labels: [],
    datasets: []
  })
  
  const chartOptions = {
    responsive: true,
    maintainAspectRatio: false,
    plugins: {
      legend: { position: 'top' },
      title: {
        display: true,
        text: 'Income & Expenses'
      }
    },
    scales: {
      y: { beginAtZero: true }
    }
  }
  
  function loadChartData() {
    const currentUser = localStorage.getItem('currentUser')

    const dummyIncome = [5000000, 4500000, 6000000, 5500000, 5200000]
    const dummyExpense = [3000000, 2500000, 4000000, 5000000, 2800000]
    const labels = ['Jan', 'Feb', 'Mar', 'Apr', 'May']
    const incomeData = [...dummyIncome]
    const expenseData = [...dummyExpense]
  
    const monthNames = ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec']
    const transactions = JSON.parse(localStorage.getItem('transactions') || '[]')
    const monthlyData = {}

    const userTransactions = transactions.filter(tx => tx.username === currentUser)

  
    userTransactions.forEach(tx => {
      const date = new Date(tx.createdAt)
      const month = date.getMonth() // 0 = Jan, 11 = Dec
      if (month >= 5 && month <= 11) {
        if (!monthlyData[month]) {
          monthlyData[month] = { income: 0, expense: 0 }
        }
        if (tx.type === 'income') {
          monthlyData[month].income += tx.amount
        } else if (tx.type === 'expense') {
          monthlyData[month].expense += tx.amount
        }
      }
    })
  
    for (let m = 5; m <= 11; m++) {
      if (monthlyData[m]) {
        labels.push(monthNames[m])
        incomeData.push(monthlyData[m].income)
        expenseData.push(monthlyData[m].expense)
      }
    }
  
    chartData.value = {
      labels,
      datasets: [
        {
          label: 'Income',
          data: incomeData,
          borderColor: '#2ecc71',
          backgroundColor: '#2ecc71',
          tension: 0.4
        },
        {
          label: 'Expenses',
          data: expenseData,
          borderColor: '#e74c3c',
          backgroundColor: '#e74c3c',
          tension: 0.4
        }
      ]
    }
  }
  
  onMounted(() => {
    loadChartData()
  })
  </script>
  

  