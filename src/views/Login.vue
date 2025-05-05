<template>
  <div class="login">
    <h1>Вход в систему</h1>
    <form @submit.prevent="handleSubmit" class="login__form">
      <div class="login__form-group">
        <label for="email">Email:</label>
        <input
          type="email"
          id="email"
          v-model="email"
          @blur="validateEmail"
          :class="{ invalid: emailError }"
        />
        <span v-if="emailError" class="login__form_error-message">{{ emailError }}</span>
      </div>

      <div class="login__form-group">
        <label for="password">Пароль:</label>
        <input
          type="password"
          id="password"
          v-model="password"
          @blur="validatePassword"
          :class="{ invalid: passwordError }"
        />
        <span v-if="passwordError" class="login__form_error-message">{{ passwordError }}</span>
      </div>

      <button type="submit" :disabled="isSubmitting">
        {{ isSubmitting ? 'Вход...' : 'Войти' }}
      </button>

      <div v-if="authError" class="login__form_auth-error">{{ authError }}</div>
    </form>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'

const router = useRouter()

const email = ref('')
const password = ref('')
const emailError = ref('')
const passwordError = ref('')
const authError = ref('')
const isSubmitting = ref(false)

// Валидация email
const validateEmail = () => {
  const re = /^[^\s@]+@[^\s@]+\.[^\s@]+$/
  if (!email.value) {
    emailError.value = 'Email обязателен'
  } else if (!re.test(email.value)) {
    emailError.value = 'Введите корректный email'
  } else {
    emailError.value = ''
  }
}

// Валидация пароля
const validatePassword = () => {
  if (!password.value) {
    passwordError.value = 'Пароль обязателен'
  } else if (password.value.length < 6) {
    passwordError.value = 'Пароль должен быть не менее 6 символов'
  } else {
    passwordError.value = ''
  }
}

// Отправка формы
const handleSubmit = async () => {
  validateEmail()
  validatePassword()

  if (emailError.value || passwordError.value) {
    return
  }

  isSubmitting.value = true
  authError.value = ''

  try {
    // Мокаем запрос к API
    const response = await mockLogin(email.value, password.value)

    if (response.success) {
      localStorage.setItem('authToken', response.token)
      router.push('/dashboard')
    } else {
      authError.value = response.message || 'Ошибка авторизации'
    }
  } catch (error) {
    authError.value = 'Произошла ошибка при подключении к серверу'
  } finally {
    isSubmitting.value = false
  }
}

// Мокаем функцию авторизации
const mockLogin = (email, password) => {
  return new Promise((resolve) => {
    setTimeout(() => {
      if (email === 'test@test.com' && password === 'qwerty123') {
        resolve({
          success: true,
          token: 'mock-jwt-token',
          user: { email, name: 'Test User' },
        })
      } else {
        resolve({
          success: false,
          message: 'Неверный email или пароль',
        })
      }
    }, 1000)
  })
}
</script>

<style lang="scss" scoped>
.login {
  max-width: 400px;
  margin: 0 auto;
  padding: 2rem;
  border: 1px solid #ddd;
  border-radius: 8px;
  margin-top: 5rem;

  &__form {
    display: flex;
    flex-direction: column;
    gap: 1rem;
  }

  &__form-group {
    display: flex;
    flex-direction: column;
    gap: 0.5rem;
  }

  &__form_error-message {
    color: red;
    font-size: 0.8rem;
  }

  &__form_auth-error {
    color: red;
    margin-top: 1rem;
    text-align: center;
  }
}

input {
  padding: 0.5rem;
  border: 1px solid #ddd;
  border-radius: 4px;
}

input.invalid {
  border-color: red;
}

button {
  padding: 0.5rem;
  background-color: #42b983;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;

  &:disbled {
    background-color: #cccccc;
    cursor: not-allowed;
  }
}
</style>
