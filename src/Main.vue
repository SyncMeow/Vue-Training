<script setup>  
    import {ref} from "vue";
    import { onMounted, onUnmounted, onUpdated } from "vue";

    onMounted(function() {
        console.log("Main component mounted");
    });

    onUpdated(function() {
        console.log("Main component updated");
    })

    onUnmounted(function() {
        console.log("Main component unmounted");
    });

    let contentArray = [
        "The quick brown fox jumps over the lazy dog.",
        "She sells seashells by the seashore.",
        "How much wood would a woodchuck chuck if a woodchuck could chuck wood?",
        "A journey of a thousand miles begins with a single step.",
        "To be or not to be, that is the question.",
        "All that glitters is not gold.",
        "The early bird catches the worm.",
        "A picture is worth a thousand words.",
        "When in Rome, do as the Romans do.",
        "Actions speak louder than words."
    ];

    let index = ref(0);

    let next = function() {
        let x = Math.floor(Math.random() * contentArray.length);
        if (x == index.value) {
            x = (x + 1) % contentArray.length;
        }
        index.value = x;    
    };

</script>
<template>
    <main>
        <h1 class="title">Main Component</h1>
        <p class="content">{{ contentArray[index] }}</p>
        <div>
            <button @click="next">Next</button>
        </div>
    </main>
</template>
<style scoped>
</style>