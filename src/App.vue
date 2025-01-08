<script setup>
import { ref } from "vue";

let className = ref("plainText");

let flag = ref(0);

let handler = function () {
    flag.value = flag.value ? 0 : 1;
    className.value =
        className.value === "plainText" ? "title highlight" : "plainText";
};

let content = [
    "There is a secret message behind this text",
    "Congratulations You found it",
];

let gender = ref(null);

let formatter = function(value){
    if (value == null) return "";

    let res = "";
    for (let i = 0; i < value.length; i++) {
        res += (!i)? value[i].toUpperCase() : value[i].toLowerCase();
    }
    return res;
};

</script>

<template>
    <main>
        <h1>Home</h1>

        <!--

        <div><input type = "text" /></div>
        <div><textarea> Write something in it. </textarea></div>

        <div>
            <input type = "checkbox" />
            <input type = "radio" />
        </div>
        
        <div>
            <select>
                <option> Option 1 </option>
                <option> Option 2 </option>
            </select>
        </div>

        -->

        Male <input type="radio" value="male" v-model="gender" />
        Female <input type="radio" value="female" v-model="gender" />
        <div> Your gender is {{ formatter(gender) }} </div>

        <div :class="className">{{ content[flag] }}</div>

        <button @click="handler">Click me</button>
    </main>
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
