<template>
    <div class="todoApp">
        <Home></Home>
        <TodoInput></TodoInput>
        <TodoListView></TodoListView>
    </div>
</template>

<script setup>
import TodoInput from "./TodoInput.vue";
import TodoListView from "./TodoListView.vue";
import Home from "./Home.vue";

</script>