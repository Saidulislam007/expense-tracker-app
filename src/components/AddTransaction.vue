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