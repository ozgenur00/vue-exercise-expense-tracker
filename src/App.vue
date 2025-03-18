<template>
  <Header :toggleDarkMode="toggleDarkMode" />
  <div class="container">
    <Balance :total="+total" />
    <IncomeExpenses :income="+income" :expense="+expense" />
    <TransactionList :transactions="transactions" @transaction-deleted="handleTransactionDeleted"
    @transaction-edited="handleTransactionEdited"/>
    <AddTransaction @transactionSubmitted="handleTransactionSubmitted"/>
  </div>
</template>

<script setup>
import Header from './components/Header.vue'
import Balance from './components/Balance.vue'
import IncomeExpenses from './components/IncomeExpenses.vue'
import TransactionList from './components/TransactionList.vue'
import AddTransaction from './components/AddTransaction.vue'
import { ref, computed, onMounted } from 'vue';
import { useToast } from 'vue-toastification'

const toast = useToast();

const transactions = ref([]);

const isDarkMode = ref(false);

onMounted(() => {
  const savedTransactions = JSON.parse(localStorage.getItem('transactions'));

  if(savedTransactions) {
    transactions.value = savedTransactions;
  }

  const savedMode = localStorage.getItem('darkMode');
  if(savedMode === 'true') {
    isDarkMode.value = true;
    document.body.classList.add('dark-mode');
  }
})

//Function to toggle dark mode
const toggleDarkMode = () => {
  isDarkMode.value = !isDarkMode.value;
  document.body.classList.toggle('dark-mode');
  localStorage.setItem('darkMode', isDarkMode.value);
}

// Get total
const total = computed(() => {
  return transactions.value.reduce((acc, transaction) => acc + transaction.amount, 0);
});

// Get income
const income = computed(() => {
  return transactions.value
    .filter(transaction => transaction.amount > 0)
    .reduce((acc, transaction) => acc + transaction.amount, 0)
    .toFixed(2);
});

// Get expenses
const expense = computed(() => {
  return transactions.value
    .filter(transaction => transaction.amount < 0)
    .reduce((acc, transaction) => acc + transaction.amount, 0)
    .toFixed(2);
});

//Add transaction
const handleTransactionSubmitted = (transactionData) => {
  transactions.value.push({
    id: generateUniqueId(),
    text: transactionData.text,
    amount: transactionData.amount,
    category: transactionData.category,
    date: transactionData.date
  })
  saveTransactionsToLocalStorage();
  toast.success('Transaction Added.')
}

//Generate unique ID
const generateUniqueId = () => {
  return Math.floor(Math.random() * 1000000)
}

//delete transaction
const handleTransactionDeleted = (id) => {
  transactions.value = transactions.value.filter((transaction) => transaction.id !== id);

  saveTransactionsToLocalStorage();
  toast.success("Transaction deleted")
}


//edit transaction
const handleTransactionEdited = (id, newText, newAmount, newCategory, newDate) => {

  transactions.value = transactions.value.map(transaction =>
    transaction.id === id
      ? { ...transaction, text: newText, amount: parseFloat(newAmount) || 0 , category: newCategory, date: newDate}
      : transaction
  );

  saveTransactionsToLocalStorage();
  toast.success("Transaction Edited.");
};


//save to localStorage
const saveTransactionsToLocalStorage = () => {
  localStorage.setItem('transactions', JSON.stringify(transactions.value));
}

</script>
