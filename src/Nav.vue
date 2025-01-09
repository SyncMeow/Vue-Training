<script setup>
    let emit = defineEmits(["handler"]);
    let func = function() {
        emit("handler");
    };
</script>
<template>
    <nav>
        <div>
            <h2> {{ title }} </h2>
        </div>

        <ul>
            <li>
                <router-link to="/">Home</router-link>
            </li>
            <li>
                <router-link to="/about">About</router-link>
            </li>
            <li>
                <router-link to="/contact">Contact</router-link>
            </li>
        </ul>

        <div>
            <button @click="func">Button</button>
        </div>
    </nav>
</template>
<style scoped>
nav {
    padding: 10px;
}
</style>