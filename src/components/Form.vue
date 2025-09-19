<template>
  <div class="container mt-5">
    <h1 class="text-center fw-bold mb-4" style="font-size: 2.5rem; letter-spacing: 1px">
      User Information Form
    </h1>

    <form @submit.prevent="submitForm">
      <div class="row mb-3">
        <div class="col-md-6">
          <label for="username" class="form-label">Username</label>
          <input
            type="text"
            class="form-control"
            id="username"
            v-model="formData.username"
            @blur="() => validateName(true)"
            @input="() => validateName(false)"
          />
          <div v-if="errors.username" class="text-danger">{{ errors.username }}</div>
        </div>

        <div class="col-md-6 col-sm-6">
          <label for="password" class="form-label">Password</label>
          <input
            type="password"
            class="form-control"
            id="password"
            v-model="formData.password"
            @blur="() => validatePassword(true)"
            @input="() => validatePassword(false)"
            :class="{ 'is-invalid': errors.password }"
          />
          <div v-if="errors.password" class="text-danger">{{ errors.password }}</div>
        </div>
      </div>
      <div class="row mb-3">
        <div class="col-md-6">
          <label for="gender" class="form-label">Gender</label>
          <select class="form-select" id="gender" v-model="formData.gender">
            <option value="" disabled>Select gender</option>
            <option value="male">Male</option>
            <option value="female">Female</option>
            <option value="other">Other</option>
          </select>
          <div v-if="errors.gender" class="text-danger">{{ errors.gender }}</div>
        </div>
        <div class="col-md-6">
          <div class="form-check mb-3">
            <input
              class="form-check-input"
              type="checkbox"
              id="resident"
              v-model="formData.isAustralian"
            />
            <label class="form-check-label" for="resident">Australian Resident?</label>
          </div>
          <div v-if="errors.resident" class="text-danger">{{ errors.resident }}</div>
        </div>
      </div>
      <div class="mb-3">
        <label for="reason" class="form-label">Reason for joining</label>
        <textarea class="form-control" id="reason" rows="3" v-model="formData.reason"></textarea>
        <div v-if="errors.reason" class="text-danger">{{ errors.reason }}</div>
      </div>
      <div class="text-center">
        <button type="submit" class="btn btn-primary me-2">Submit</button>
        <button type="button" class="btn btn-secondary" @click="clearForm">Clear</button>
      </div>
    </form>
    <DataTable
      :value="submittedCards"
      class="mt-5"
      v-if="submittedCards.length"
      responsiveLayout="scroll"
    >
      <Column field="username" header="Username"></Column>
      <Column field="password" header="Password"></Column>
      <Column
        field="isAustralian"
        header="Australian Resident"
        :body="(row) => (row.isAustralian ? 'Yes' : 'No')"
      ></Column>
      <Column field="gender" header="Gender"></Column>
      <Column field="reason" header="Reason"></Column>
    </DataTable>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const submittedCards = ref([])

const formData = ref({
  username: '',
  password: '',
  isAustralian: false,
  gender: '',
  reason: '',
})

const submitForm = () => {
  const validName = validateName(true)
  const validPassword = validatePassword(true)
  const validGender = validateGender()
  const validResident = validateResident()
  const validReason = validateReason()

  // Only submit if all validations pass
  if (validName && validPassword && validGender && validResident && validReason) {
    submittedCards.value.push({ ...formData.value })
    clearForm()
  }
}

const clearForm = () => {
  formData.value = {
    username: '',
    password: '',
    isAustralian: false,
    gender: '',
    reason: '',
  }
}

const errors = ref({
  username: null,
  password: null,
  resident: null,
  gender: null,
  reason: null,
})

// Validate username input
const validateName = (onBlur = false) => {
  if (!formData.value.username) {
    errors.value.username = 'Username is required'
  } else if (formData.value.username.length < 3) {
    errors.value.username = 'Username must be at least 3 characters'
  } else {
    errors.value.username = null
  }

  if (onBlur && errors.value.username) {
    return false
  }
  return true
}

// Validate password input
const validatePassword = (onBlur = false) => {
  const password = formData.value.password
  const minLength = 8
  const hasUppercase = /[A-Z]/.test(password)
  const hasLowercase = /[a-z]/.test(password)
  const hasNumber = /\d/.test(password)
  const hasSpecialChar = /[!@#$%^&*(),.?":{}|<>]/.test(password)

  if (!password) {
    errors.value.password = 'Password is required'
    return false
  } else if (password.length < minLength) {
    errors.value.password = `Password must be at least ${minLength} characters long.`
    return false
  } else if (!hasUppercase) {
    errors.value.password = 'Password must contain at least one uppercase letter.'
    return false
  } else if (!hasLowercase) {
    errors.value.password = 'Password must contain at least one lowercase letter.'
    return false
  } else if (!hasNumber) {
    errors.value.password = 'Password must contain at least one number.'
    return false
  } else if (!hasSpecialChar) {
    errors.value.password = 'Password must contain at least one special character.'
    return false
  } else {
    errors.value.password = null
    return true
  }
}

// Validate gender selection
const validateGender = () => {
  if (!formData.value.gender) {
    errors.value.gender = 'Gender is required'
    return false
  }
  errors.value.gender = null
  return true
}

// Validate if Australian resident checkbox is checked
const validateResident = () => {
  if (!formData.value.isAustralian) {
    errors.value.resident = 'You must be an Australian resident'
    return false
  }
  errors.value.resident = null
  return true
}

// Validate reason input
const validateReason = () => {
  if (!formData.value.reason) {
    errors.value.reason = 'Reason is required'
    return false
  }
  errors.value.reason = null
  return true
}
</script>
