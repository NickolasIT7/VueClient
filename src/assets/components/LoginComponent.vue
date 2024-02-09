<template>
    <form @submit.prevent="loginFunc">
        <div style="text-align: center;">
          <button id="login" @click.prevent="loginClick">login</button>
          <button id="reg" @click.prevent="regClick">registration</button>
        </div>
        <input id="mail" type="mail" v-model="mail" placeholder="mail">
        <input id="pass" type="password" v-model="pass" placeholder="pass">
        <input v-if="action=='reg'" id="pass2" type="password"  v-model="pass2" placeholder="retype pass">
        <br>
        <input id="submit" type="submit" @click.prevent="loginFunc" :value="submitVal">
      </form>
  </template>
  
  <script setup lang="ts">
  import router from '@/router'
  import md5 from 'md5'
  import axios from 'axios'
  import {ref} from 'vue'
  
  const mail = ref('')
  const pass = ref('')
  const pass2 = ref('')
  const action = ref('login')
  const submitVal = ref('Log In')
  
  const loginClick = () => {
    action.value = 'login'
    actionChange()
  }
  const regClick = () => {
    action.value = 'reg'
    actionChange()
  }
  
  function actionChange() {
    if (action.value == 'login') {
      submitVal.value = 'Log In'
    } else {
      submitVal.value = 'Register'
    }
  }
  
  async function loginFunc() {
    if (!mail.value) {
      alert('input mail')
      return
    }
    if (!pass.value) {
      alert('input pass')
      return
    }
    try {
      if (action.value == 'login') {
        const data = await axios.post('http://localhost:3001/login', {
          email: mail.value,
          hash: md5(pass.value).toString()
        })
        console.log(data)
        if (!data.data) {
          alert('check mail and pass')
          return
        }
        localStorage.id = data.data.id
        localStorage.uuid = data.data.uuid
        router.push('/')
      } else {
        if (pass.value != pass2.value) {
          alert('pass not eqals')
          return
        }
        const data = await axios.post('http://localhost:3001/create_user', {
          email: mail.value,
          hash: md5(pass.value).toString()
        })
        console.log(data)
        localStorage.id = data.data.id
        localStorage.uuid = data.data.uuid
        router.push('/')
      }
    } catch (e) {
      console.log(e)
    }
  }
  </script>
  
  <style scoped>
  body {
    margin: 0 auto;
    padding: 0;
    display: flex;
    align-items: center;
    height: 100vh;
    max-width: 300px;
    background-color: antiquewhite;
  }
  </style>