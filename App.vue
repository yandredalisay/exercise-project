<template>
  <div id="app">
    <h1>User Management</h1>
    
    <form @submit.prevent="isEditing ? updateUser() : addUser()">
      <div class="form-group">
        <input 
          v-model="currentUser.name" 
          :class="{ 'error': formErrors.name }"
          placeholder="Name" 
          required 
        />
        <span v-if="formErrors.name" class="error-text">Name is required</span>
      </div>

      <div class="form-group">
        <input 
          v-model="currentUser.email" 
          :class="{ 'error': formErrors.email }"
          placeholder="Email" 
          required 
        />
        <span v-if="formErrors.email" class="error-text">Valid email is required</span>
      </div>

      <button type="submit" :class="{ 'edit-mode': isEditing }">
        {{ isEditing ? 'Update User' : 'Add User' }}
      </button>
      <button 
        type="button" 
        v-if="isEditing" 
        @click="cancelEdit"
        class="cancel-button"
      >
        Cancel
      </button>
    </form>

    <div v-if="users.length === 0" class="empty-state">
      No users found. Add some users to get started!
    </div>

    <div class="user-list" v-else>
      <div 
        v-for="user in users" 
        :key="user.id" 
        class="user-card"
        :class="{ 'selected': currentUser.id === user.id }"
      >
        <h2>{{ user.name }}</h2>
        <p>{{ user.email }}</p>
        <div class="button-group">
          <button 
            @click="editUser(user)"
            :disabled="isEditing && currentUser.id !== user.id"
          >
            Edit
          </button>
          <button 
            @click="deleteUser(user.id)"
            :disabled="isEditing"
            class="delete-button"
          >
            Delete
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      users: [],
      currentUser: {
        id: null,
        name: '',
        email: ''
      },
      isEditing: false,
      formErrors: {
        name: false,
        email: false
      }
    }
  },
  computed: {
    hasErrors() {
      return this.formErrors.name || this.formErrors.email
    }
  },
  methods: {
    validateForm() {
      this.formErrors.name = !this.currentUser.name
      this.formErrors.email = !this.currentUser.email || !this.validateEmail(this.currentUser.email)
      return !this.hasErrors
    },
    validateEmail(email) {
      const re = /^[^\s@]+@[^\s@]+\.[^\s@]+$/
      return re.test(email)
    },
    addUser() {
      if (this.validateForm()) {
        this.currentUser.id = Date.now()
        this.users.push({ ...this.currentUser })
        this.resetForm()
      }
    },
    editUser(user) {
      this.currentUser = { ...user }
      this.isEditing = true
    },
    updateUser() {
      if (this.validateForm()) {
        const index = this.users.findIndex(user => user.id === this.currentUser.id)
        if (index !== -1) {
          this.users.splice(index, 1, { ...this.currentUser })
          this.resetForm()
        }
      }
    },
    deleteUser(id) {
      if (confirm('Are you sure you want to delete this user?')) {
        this.users = this.users.filter(user => user.id !== id)
      }
    },
    cancelEdit() {
      this.resetForm()
    },
    resetForm() {
      this.currentUser = {
        id: null,
        name: '',
        email: ''
      }
      this.isEditing = false
      this.formErrors = {
        name: false,
        email: false
      }
    }
  }
}
</script>

<style>
#app {
  font-family: Arial, sans-serif;
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
}

h1 {
  color: #333;
  text-align: center;
}

.form-group {
  display: flex;
  flex-direction: column;
  margin-bottom: 10px;
}

form {
  display: flex;
  flex-direction: column;
  gap: 10px;
  max-width: 400px;
  margin: 0 auto 20px auto;
  padding: 20px;
  background-color: #f5f5f5;
  border-radius: 8px;
}

input {
  padding: 8px;
  border: 1px solid #ddd;
  border-radius: 4px;
  width: 100%;
}

input.error {
  border-color: #f44336;
}

.error-text {
  color: #f44336;
  font-size: 0.8em;
  margin-top: 4px;
}

button {
  padding: 8px 16px;
  background-color: #4CAF50;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.3s;
}

button:disabled {
  background-color: #cccccc;
  cursor: not-allowed;
}

button:hover:not(:disabled) {
  background-color: #45a049;
}

.cancel-button {
  background-color: #f44336;
}

.cancel-button:hover {
  background-color: #da190b;
}

.user-list {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
  gap: 20px;
  padding: 20px;
}

.user-card {
  border: 1px solid #ccc;
  padding: 15px;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  background-color: white;
  transition: transform 0.2s, box-shadow 0.2s;
}

.user-card.selected {
  border-color: #4CAF50;
  box-shadow: 0 0 8px rgba(76, 175, 80, 0.5);
}

.user-card:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.15);
}

.button-group {
  display: flex;
  gap: 10px;
  margin-top: 10px;
}

.delete-button {
  background-color: #f44336;
}

.delete-button:hover:not(:disabled) {
  background-color: #da190b;
}

.empty-state {
  text-align: center;
  color: #666;
  padding: 40px;
  font-style: italic;
}
</style>