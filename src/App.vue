<template>
    <Header />
    <div class="container">
        <Balance :total="total" />
        <IncomeExpense :income="+income" :expenses="+expenses"/>
        <TransactionList :transactions="transactions" @transactionDeleted="handleTransactionDeleted"/> 
        <AddTransaction @transactionAdded="handleTransactionAdded"/>
    </div>
</template>

<script setup>
import Header from './components/Header.vue';
import Balance from './components/Balance.vue';
import IncomeExpense from './components/IncomeExpense.vue';
import TransactionList from './components/TransactionList.vue';``
import AddTransaction from './components/AddTransaction.vue';

import { useToast } from 'vue-toastification';
import { ref, computed, onMounted } from 'vue';

const toast = useToast();
const transactions = ref([]);

onMounted(() => {
    const savetransactions = JSON.parse(localStorage.getItem('transactions'));

    if (savetransactions) {
        transactions.value = savetransactions;
    }
});

// Get total
const total = computed(() => {
    return transactions.value.reduce((acc, transaction) => {
        return acc += transaction.amount;
    }, 0);
});

// Get income
const income = computed(() => {
    return transactions.value
    .filter((transaction) => transaction.amount > 0)
    .reduce((acc, transaction) => {
        return acc += transaction.amount;
    }, 0)
    .toFixed(2);
});

// Get expenses
const expenses = computed(() => {
    return transactions.value
    .filter((transaction) => transaction.amount < 0)
    .reduce((acc, transaction) => {
        return acc += transaction.amount;
    }, 0)
    .toFixed(2);
});


// Add transaction
const handleTransactionAdded = (transactionData) => {
    transactions.value.push({
        id: generateUniqueId(),
        text: transactionData.text,
        amount: transactionData.amount,
    });
    saveTransactionsToLocalStorage();
    toast.success('Transaction added successfully.');
};

// Generate unique id
const generateUniqueId = () => {
    return Math.floor(Math.random() * 1000000);
};

// Delete transaction
const handleTransactionDeleted = (id) => {
    transactions.value = transactions.value.filter((transaction) => transaction.id !== id);
    saveTransactionsToLocalStorage();
    toast.success('Transaction deleted successfully.');
};

// Save transactions to local storage
const saveTransactionsToLocalStorage = () => {
    localStorage.setItem('transactions', JSON.stringify(transactions.value));
}

</script>