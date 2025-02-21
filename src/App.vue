<template>
  <div class="container">
    <h2>Expense Tracker</h2>
    <h3>Add Transaction</h3>
    <input type="text" placeholder="Title" v-model="title">
    <input type="number" placeholder="Amount" v-model="amount">
    <select v-model="type">
      <option value="Income">Income</option>
      <option value="Expense">Expense</option>
    </select>
    <button @click="addTransaction">Add</button>

    <h2>Transaction List</h2>
    <select v-model="filterType">
      <option value="All">All</option>
      <option value="Income">Income</option>
      <option value="Expense">Expense</option>
    </select>

    <table>
      <thead>
        <tr>
          <th>Title</th>
          <th>Amount</th>
          <th>Type</th>
          <th>Action</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(transaction, index) in paginatedTransactions" :key="index">
          <td>{{ transaction.title }}</td>
          <td :class="amountClass(transaction.amount)">{{ transaction.amount }}</td>
          <td :class="typeClass(transaction.type)">{{ transaction.type }}</td>
          <td><button @click="deleteTransaction(index)">Delete</button></td>
        </tr>
      </tbody>
    </table>

    <!-- Pagination Buttons -->
    <div class="pagination">
      <button @click="prevPage" :disabled="currentPage === 1">Previous</button>
      <span>Page {{ currentPage }} of {{ totalPages }}</span>
      <button @click="nextPage" :disabled="currentPage === totalPages">Next</button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      title: "",
      amount: "",
      type: "Income",
      filterType: "All",
      transactions: [],
      currentPage: 1,
      itemsPerPage: 5
    };
  },
  computed: {
    filteredTransactions() {
      if (this.filterType === "All") return this.transactions;
      return this.transactions.filter((t) => t.type === this.filterType);
    },
    paginatedTransactions() {
      const start = (this.currentPage - 1) * this.itemsPerPage;
      const end = start + this.itemsPerPage;
      return this.filteredTransactions.slice(start, end);
    },
    totalPages() {
      return Math.ceil(this.filteredTransactions.length / this.itemsPerPage);
    }
  },
  methods: {
    addTransaction() {
      if (this.title && this.amount) {
        this.transactions.push({
          title: this.title,
          amount: parseFloat(this.amount),
          type: this.type
        });
        this.title = "";
        this.amount = "";
      }
    },
    deleteTransaction(index) {
      this.transactions.splice(index, 1);
    },
    amountClass(amount) {
      return {
        "text-success": amount > 0,
        "text-danger fw-bold": amount >= 500
      };
    },
    typeClass(type) {
      return {
        "text-uppercase": true,
        "text-success": type === "Income",
        "text-danger": type === "Expense"
      };
    },
    nextPage() {
      if (this.currentPage < this.totalPages) {
        this.currentPage++;
      }
    },
    prevPage() {
      if (this.currentPage > 1) {
        this.currentPage--;
      }
    }
  }
};
</script>