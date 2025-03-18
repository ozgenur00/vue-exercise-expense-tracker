<template>
    <h3>History</h3>
    <ul id="list" class="list">
      <li v-for="transaction in transactions" 
          :key="transaction.id" 
          :class="transaction.amount < 0 ? 'minus' : 'plus'">
        
        <!-- Show input fields when in edit mode -->
        <div v-if="editingTransactionId === transaction.id">
            <input type="text" v-model="editedText" />
            <input type="number" v-model.number="editedAmount" />
                <select id="category" v-model="editedCategory">
                <option value="Restaurant">Restaurant</option>
                <option value="Grocery">Grocery</option>
                <option value="Health">Health</option>
                <option value="Shopping">Shopping</option>
                <option value="Travel">Travel</option>
                <option value="Bills">Bills</option>
                <option value="Entertainment">Entertainment</option>
                <option value="Other">Other</option>
                </select>
                <input type="date" v-model="editedDate">
            <button @click="saveEdit(transaction.id)" class="save-btn">Save</button>
        </div>

        <div v-else>
            <div class="transaction-main">
                {{ transaction.text }} - <span>${{ transaction.amount }}</span>
            </div>
            <div class="transaction-meta">
                {{ transaction.category }} - {{ transaction.date }}
            </div>
        </div>
        
        <button @click="deleteTransaction(transaction.id)" class="delete-btn">x</button>
        <button v-if="editingTransactionId !== transaction.id" @click="startEditing(transaction)" class="edit-btn">Edit</button>
      </li>
    </ul>
</template>
  
<script setup>
import { ref, defineProps, defineEmits } from 'vue';

defineProps({
  transactions: {
    type: Array,
    required: true,
  },
});

const emit = defineEmits(['transactionDeleted', 'transactionEdited']);

// Track which transaction is being edited
const editingTransactionId = ref(null);
const editedText = ref("");
const editedAmount = ref(null);
const editedCategory = ref("");
const editedDate = ref("");

// Function to enable editing mode
const startEditing = (transaction) => {
    editingTransactionId.value = transaction.id;
    editedText.value = transaction.text;
    editedAmount.value = transaction.amount; 
    editedCategory.value = transaction.category;
    editedDate.value = transaction.date;
}

// Function to save edited transaction
const saveEdit = (id) => {
    emit('transactionEdited', id, editedText.value, parseFloat(editedAmount.value) || 0, editedCategory.value, editedDate.value);
    editingTransactionId.value = null; 
}

const deleteTransaction = (id) => {
    emit('transactionDeleted', id);
}
</script>
