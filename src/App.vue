<script setup>
import {ref} from "vue";
import MainComp from "./Main.vue";

let visible = ref(true);

let hide = function() {
    visible.value = false;
};

let show = function() {
    visible.value = true;
};

</script>

<template>
    <MainComp v-if="visible"></MainComp>
    <button @click="hide">Hide</button>
    <button @click="show">Show</button>
</template>

<style scoped>
main {
    background-color: white;
}

.title {
    font-weight: bold;
}

.highlight {
    color: red;
}

.content {
    color: blue;
}
</style>
