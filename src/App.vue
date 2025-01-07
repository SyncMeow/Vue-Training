<script setup>
    let message = "<u>Hello Vue</u>";
    let name = "content";
    let level = 1;
    let topics = ["JavaScript", "CSS", "HTML", "Vue.js", "Djangle", "MariaDB"];
    let contentDict = {Frontend: "Vue.js", Backend: "Django.py", Database: "MariaDB"};
</script>

<template>
    <nav>Navigation</nav>
    <main v-html = "message.toUpperCase()"></main>
    <div :class = "name"> This is a message. </div>
    <div v-if = "level >= 3"> You have expertised it. </div>
    <div v-else-if = "level == 2"> You are quite good at it. </div>
    <div v-else> 
        <div>
            <h3> God damn you are such a noob. </h3>
            <h4> You should start from the following topics. </h4>
        </div>
        <ul>
            <!-- <li v-for = "(name, index) in topics">{{ index+1 }} : {{ name }}</li> -->
            <li v-for = "(name, key) in contentDict">{{ key }} : {{ name }}</li>
        </ul>
    </div>
</template>

<style scoped>
    main{background-color: white;}
    .content{color: blue;}
</style>