<template>
    <h2>Expense Tracker</h2>
    <label class="toggle-switch">
        <input type="checkbox" @change="toggleDarkMode" :checked="isDarkMode" />
        <span class="slider"></span>
    </label>
</template>


<script setup>
defineProps(['toggleDarkMode', "isDarkMode"])</script>