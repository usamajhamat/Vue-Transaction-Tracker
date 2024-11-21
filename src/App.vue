<template>
  <Header/>
  <div class="container">
    <Balance :total="+total"/>
    <IncomeExpense :income="+income" :expense="+expense"/>
    <TransectionList :transactions="transactions" @transactionDeleted="handleTransactionDeleted" />
    <AddTransection @trasactionSubmitted="handleTransactionSubmitted"/>
  </div>
</template>

<script setup>
  import Header from './components/Header.vue'
  import Balance from './components/Balance.vue'
  import IncomeExpense from './components/IncomeExpense.vue'
  import TransectionList from './components/TransectionList.vue'
  import AddTransection from './components/AddTransection.vue'
  import { ref, computed,onMounted } from 'vue'
  import { useToast } from 'vue-toastification'

  onMounted(()=>{
    const savedTransactions = JSON.parse(localStorage.getItem('transactions'))
    if(savedTransactions){
      transactions.value = savedTransactions
    }
  })

  const transactions = ref([]);


  const toast = useToast()

  const total = computed(() => {
    return transactions.value.reduce((acc, transaction) => {
      return acc + transaction.amount;
    }, 0);
  });

  //Income
  const income = computed(() => {
    return transactions.value
    .filter((transaction)=> transaction.amount > 0)
    .reduce((acc, transaction) => {
      return acc + transaction.amount;
    }, 0).toFixed(2);
  });

  //Expense
  const expense = computed(() => {
    return transactions.value
    .filter((transaction)=> transaction.amount < 0)
    .reduce((acc, transaction) => {
      return acc + transaction.amount;
    }, 0).toFixed(2);
  });

  //Handle Transaction

  const handleTransactionSubmitted = (transactionData)=>{
    console.log('Transaction Data-------', transactionData);
    transactions.value.push({
      id: generateUniqueId(),
      text: transactionData.text,
      amount:transactionData.amount
    })
    console.log('ID----', generateUniqueId());
    saveTransactionsToLocalStorage()
    toast.success('Transaction Added Successfully')
  }

  //Generate Unique Id

  const generateUniqueId = () =>{
    return Math.floor(Math.random()* 1000000)
  }

  const handleTransactionDeleted = (id) =>{
    console.log('Deleted_ID----', id);
    transactions.value = transactions.value.filter((transaction) =>transaction.id !== id)
    saveTransactionsToLocalStorage()
    toast.success('Transaction Deleted Successfully')
  }

  const saveTransactionsToLocalStorage = () =>{
    localStorage.setItem('transactions', JSON.stringify(transactions.value))
  }


</script>
