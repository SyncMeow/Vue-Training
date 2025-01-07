<script setup>
    let message = "<u>Hello Vue</u>";
    let name = "content";
</script>

<template>
    <nav>Navigation</nav>
    <main v-html = "message.toUpperCase()"></main>
    <div :class = "name"> This is a message. </div>
</template>

<style scoped>
    main{background-color: white;}
    .content{color: blue;}
</style>