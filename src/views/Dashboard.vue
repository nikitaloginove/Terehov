<template>
  <div class="dashboard">
    <h1>Добро пожаловать!</h1>
    <p>Вы успешно вошли в систему.</p>
    <button @click="handleLogout">Выйти</button>
  </div>
</template>

<script setup>
import { useRouter } from 'vue-router'

const router = useRouter()

const handleLogout = () => {
  localStorage.removeItem('authToken')
  router.push('/')
}
</script>

<style lang="scss" scoped>
.dashboard {
  max-width: 600px;
  margin: 0 auto;
  padding: 2rem;
  text-align: center;
}

button {
  padding: 0.5rem 1rem;
  background-color: #ff4444;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  margin-top: 1rem;
}
</style>
