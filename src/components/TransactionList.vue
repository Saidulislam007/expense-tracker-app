<template>
  <div>
    <h2 class="text-center">Add Transaction</h2>
    <form @submit.prevent="addTransaction">
      <div class="mb-3">
        <label>Title:</label>
        <input v-model="title" type="text" class="form-control" required />
      </div>

      <div class="mb-3">
        <label>Amount:</label>
        <input v-model.number="amount" type="number" class="form-control" required min="1" />
      </div>

      <div class="mb-3">
        <label>Type:</label>
        <select v-model="type" class="form-select">
          <option value="Income">Income</option>
          <option value="Expense">Expense</option>
        </select>
      </div>

      <button class="btn btn-primary" type="submit">Add</button>
    </form>

    <p v-if="errorMessage" class="text-danger mt-2">{{ errorMessage }}</p>
  </div>
</template>

<script setup>
import { ref } from "vue";

const title = ref("");
const amount = ref(null);
const type = ref("Income");
const errorMessage = ref("");

const transactions = ref(JSON.parse(localStorage.getItem("transactions")) || []);

const addTransaction = () => {
  if (!title.value || amount.value <= 0) {
    errorMessage.value = "Amount must be greater than 0!";
    return;
  }

  transactions.value.push({ title: title.value, amount: amount.value, type: type.value });
  localStorage.setItem("transactions", JSON.stringify(transactions.value));

  title.value = "";
  amount.value = null;
  type.value = "Income";
  errorMessage.value = "";
};
</script>
You sent
<template>
  <div>
    <h2 class="text-center">Transaction List</h2>
    <div class="mb-3">
      <label>Filter:</label>
      <select v-model="filterType" class="form-select">
        <option value="all">All</option>
        <option value="income">Income</option>
        <option value="expense">Expense</option>
      </select>
    </div>

    <table class="table table-bordered">
      <thead>
        <tr>
          <th>Title</th>
          <th>Amount</th>
          <th>Type</th>
          <th>Action</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(transaction, index) in filteredTransactions" :key="index">
          <td>{{ transaction.title }}</td>
          <td :class="amountClass(transaction)">${{ transaction.amount }}</td>
          <td :class="typeClass(transaction)">{{ transaction.type.toUpperCase() }}</td>
          <td>
            <button class="btn btn-danger" @click="deleteTransaction(index)">Delete</button>
          </td>
        </tr>
        <tr v-if="transactions.length === 0">
          <td colspan="4" class="text-center">No transactions recorded yet.</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script setup>
import { ref, computed } from "vue";

const transactions = ref(JSON.parse(localStorage.getItem("transactions")) || []);
const filterType = ref("all");

const filteredTransactions = computed(() => {
  if (filterType.value === "income") return transactions.value.filter(t => t.type === "Income");
  if (filterType.value === "expense") return transactions.value.filter(t => t.type === "Expense");
  return transactions.value;
});

const deleteTransaction = (index) => {
  transactions.value.splice(index, 1);
  localStorage.setItem("transactions", JSON.stringify(transactions.value));
};

const amountClass = (transaction) => {
  return {
    "text-danger fw-bold": transaction.amount >= 500,
    "text-success": transaction.type === "Income",
    "text-danger": transaction.type === "Expense"
  };
};

const typeClass = (transaction) => {
  return transaction.type === "Income" ? "text-success" : "text-danger";
};
</script>