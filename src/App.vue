<template>
  <h1>Mon application</h1>
  <form action="" method="post" @submit.prevent="onSubmit">
    <fieldset>
      <legend>Connexion</legend>
      <div>
        <label for="nom">Nom</label>
        <input type="nom" name="nom" id="nom" placeholder="james">
      </div>
      <div>
        <label for="email">Email</label>
        <input type="email" name="email" id="emailInput" placeholder="jamesBond@gmail.com" @blur="onBlurEmail($event)">
        <p class="error">
          {{ messageEmail }}
        </p>
      </div>
      <div>
        <label for="password">Password</label>
        <input type="password" name="password" id="password" placeholder="azerty" @blur="onBlurPassword($event)">
        <p class="error">
          {{ messagePassword }}
        </p>
      </div>
      <div>
        <label for="password">Password</label>
        <input type="password" name="password" id="passwordConf" placeholder="azerty"
          @blur="onBlurConfirmationPassword($event)">
        <p class="error">
          {{ messageConfirmationPassword }}
        </p>
      </div>
      <p>
        <button type="submit" :disabled="disabled"><b>Se connecter</b></button>
      </p>
    </fieldset>
  </form>
</template>

<script setup>
import { ref, computed } from 'vue'
import CryptoJS from 'crypto-js';

const messageEmail = ref('')
const messagePassword = ref('')
const messageConfirmationPassword = ref('')

const disabled = computed(() => {
  return messageEmail.value != '' || messagePassword.value != '' || messageConfirmationPassword.value != ''
})

function md5(input) {
  return CryptoJS.MD5(input).toString();
}

const onBlurEmail = ($event) => {
  console.log(event.target.value)
  if (!isValidEmail(event.target.value)) {
    messageEmail.value = 'Email is not valid'
    console.log('Email is not valid')
  }
  else {
    messageEmail.value = ''
    console.log('Email is valid')
  }
  function isValidEmail(email) {
    return /^(([^<>()\[\]\\.,;:\s@"]+(\.[^<>()\[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/.test(email);
  }
}

const onBlurPassword = ($event) => {
  console.log(event.target.value)
  if (!isValidPassword(event.target.value)) {
    messagePassword.value = 'Password must be at least 8 characters'
    console.log('Password must be at least 8 characters')
  }
  else {
    messagePassword.value = ''
    console.log('Password is valid')
  }
  function isValidPassword(password) {
    return password.length >= 8;
  }
}

const onBlurConfirmationPassword = ($event) => {
  console.log(event.target.value)
  if (!isValidConfirmationPassword(document.querySelector('#password').value, event.target.value)) {
    messageConfirmationPassword.value = 'Password must be the same'
    console.log('Confirmation password must be the same')
  }
  else {
    messageConfirmationPassword.value = ''
    console.log('Confirmation password is valid')
  }
  function isValidConfirmationPassword(password, confPassword) {
    return password == confPassword;
  }
}

function onSubmit() {
  const nomInput = document.querySelector('#nom').value;
  const emailInput = document.querySelector('#emailInput').value;
  const passwordInput = document.querySelector('#password').value;

  return fetch('/api/users/token', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      login: nomInput,
      email: emailInput,
      password: md5(passwordInput)
    })
  })
    .then(response => response.json())
    .then(data => {
      console.log(data)
      return data
    })
    .catch(error => console.error(error))
}

</script>

<style scoped>
.error {
  color: rgb(154, 0, 0);
}
</style>