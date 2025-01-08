<template>
    <div class="login">
      <h1>Login</h1>
      <form @submit.prevent="login">
        <div>
          <label for="email">Email:</label>
          <input type="email" v-model="email" required />
        </div>
        <div>
          <label for="password">Password:</label>
          <input type="password" v-model="password" required />
        </div>
        <button type="submit">Login</button>
      </form>
      <p v-if="error">{{ error }}</p>
    </div>
  </template>
  
  <script>
  import axios from "axios";
  
  export default {
    data() {
      return {
        email: "",
        password: "",
        error: null,
      };
    },
    methods: {
      async login() {
        try {
          const response = await axios.post("http://localhost:5000/api/login", {
            email: this.email,
            password: this.password,
          });
          console.log("Login successful:", response.data);
          localStorage.setItem("token", response.data.token); // Save JWT
        } catch (err) {
          this.error = err.response?.data?.message || "Login failed";
        }
      },
    },
  };
  </script>
  
  <style scoped>
  .login {
    max-width: 300px;
    margin: auto;
  }
  </style>
  