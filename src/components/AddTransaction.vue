<template>
    <h3>Add new transaction</h3>
    <form id="form" @submit.prevent="onSubmit">
        <div class="form-control">
            <label for="text">Text</label>
            <input type="text" id="text" v-model="text" placeholder="Enter Text..." />
        </div>
        <div class="form-control">
            <label for="amount">Amount <br />
            (negative - expense, positive - income)</label>
            <input type="text" id="amount" v-model="amount" placeholder="Enter amount..."/>
        </div>
        <div class="form-control">
            <label for="category">Category</label><br />
                <select id="category" v-model="category">
                <option value="Restaurant">Restaurant</option>
                <option value="Grocery">Grocery</option>
                <option value="Health">Health</option>
                <option value="Shopping">Shopping</option>
                <option value="Travel">Travel</option>
                <option value="Bills">Bills</option>
                <option value="Entertainment">Entertainment</option>
                <option value="Other">Other</option>
                </select>
        </div>
        <div class="form-control">
            <label for="date">Date</label><br />
            <input type="date" id="date" v-model="date"/>
        </div>
        <button class="btn">Add transaction</button>
    </form>
</template>

<script setup>
import { ref } from 'vue';
import {useToast} from 'vue-toastification'

const text = ref('');
const amount = ref('');

const emit = defineEmits(['transactionSubmitted']);

const toast = useToast();

const category = ref("Grocery");
const date = ref('');


const onSubmit = () => {
        if (!text.value || !amount.value) {
            toast.error("Both fields must be filled")
            return;
        } 

        const transactionData = {
            text: text.value,
            amount: parseFloat(amount.value),
            category: category.value,
            date: date.value
        }

        emit("transactionSubmitted", transactionData);

        text.value = '';
        amount.value = '';
}
</script>