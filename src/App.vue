<template>
  <div class="container">
    <h2 class="text-center title">Create an Account</h2>

    <!-- Registration Form -->
    <div class="card">
      <form @submit.prevent="submitForm">
        <div class="form-group">
          <label>Name:</label>
          <input v-model="name" class="form-control" placeholder="Enter name">
          <small v-if="errors.name" class="error">{{ errors.name }}</small>
        </div>

        <div class="form-group">
          <label>Email:</label>
          <input v-model="email" type="email" class="form-control" placeholder="Enter email">
          <small v-if="errors.email" class="error">{{ errors.email }}</small>
        </div>

        <div class="form-group">
          <label>Password:</label>
          <input v-model="password" type="password" class="form-control" placeholder="Enter password">
          <small v-if="errors.password" class="error">{{ errors.password }}</small>
        </div>

        <div class="form-group">
          <label>Age:</label>
          <input v-model="age" type="number" class="form-control" placeholder="Enter age">
          <small v-if="errors.age" class="error">{{ errors.age }}</small>
        </div>

        <button type="submit" class="btn btn-success w-100">Register</button>
      </form>
    </div>

    <!-- Loading Spinner -->
    <div v-if="loading" class="text-center mt-3">
      <div class="spinner-border text-primary"></div>
    </div>

    <!-- User List Table -->
    <h3 class="mt-4 text-center">Registered Users</h3>
    <table class="table table-bordered">
      <thead class="thead-green">
        <tr>
          <th>Name</th>
          <th>Email</th>
          <th>Age</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="user in users" :key="user.id">
          <td>{{ user.name }}</td>
          <td>{{ user.email }}</td>
          <td>{{ user.age }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      name: "",
      email: "",
      password: "",
      age: "",
      users: [],
      errors: {},
      loading: false,
    };
  },
  methods: {
    validateForm() {
      this.errors = {};

      if (this.name.length < 3) {
        this.errors.name = "Name must be at least 3 characters.";
      }
      if (!/\S+@\S+\.\S+/.test(this.email)) {
        this.errors.email = "Invalid email format.";
      }
      if (this.password.length < 6) {
        this.errors.password = "Password must be at least 6 characters.";
      }
      if (!this.age || this.age < 18) {
        this.errors.age = "Age must be at least 18.";
      }

      // Check if email already exists
      if (this.users.some(user => user.email === this.email)) {
        this.errors.email = "Email is already registered.";
      }

      return Object.keys(this.errors).length === 0;
    },
    submitForm() {
      if (!this.validateForm()) {
        return;
      }

      const newUser = {
        id: this.users.length + 1,
        name: this.name,
        email: this.email,
        age: this.age,
      };

      
      axios.post("https://jsonplaceholder.typicode.com/users", newUser)
        .then(() => {
          this.users.push(newUser);
          alert("User registered successfully!");
        })
        .catch((error) => {
          console.error("Error submitting form:", error);
        });

   
      this.name = "";
      this.email = "";
      this.password = "";
      this.age = "";
    },
    fetchUsers() {
      this.loading = true;
      axios.get("https://jsonplaceholder.typicode.com/users")
        .then((response) => {
          this.users = response.data.map(user => ({
            id: user.id,
            name: user.name,
            email: user.email,
            age: Math.floor(Math.random() * 40) + 18, // Fake age
          }));
          this.loading = false;
        })
        .catch((error) => {
          console.error("Error fetching users:", error);
          this.loading = false;
        });
    }
  },
  mounted() {
    this.fetchUsers();
  }
};
</script>

<style>
/* Shopee Green Theme */
body {
  background-color: #d4edda; /* Light green */
  font-family: Arial, sans-serif;
}

.container {
  max-width: 600px;
  margin: auto;
}

.title {
  color: #28a745; /* Green title */
  font-weight: bold;
}

.card {
  padding: 20px;
  background: white;
  border-radius: 10px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

label {
  font-size: 18px;
  font-weight: bold;
  color: #155724;
}

input {
  width: 100%;
  padding: 10px;
  margin: 5px 0;
  font-size: 16px;
  border-radius: 5px;
  border: 1px solid #28a745;
}

.btn-success {
  background-color: #28a745;
  border: none;
  padding: 10px;
  font-size: 18px;
  border-radius: 5px;
}

.btn-success:hover {
  background-color: #218838;
}

.error {
  color: red;
  font-size: 14px;
  margin-left: 10px;
}

/* Table Styles */
.table {
  background: white;
  margin-top: 20px;
}

.thead-green {
  background-color: #28a745;
  color: white;
}

th, td {
  padding: 10px;
  text-align: center;
}
</style>
