<script setup>
import { ref } from 'vue'

const mode = ref('login')
const transitionName = ref('auth-forward')
const name = ref('')
const email = ref('')
const phone = ref('')
const password = ref('')
const passwordConfirm = ref('')
const stage = ref('credentials')
const otp = ref('')
const otpError = ref('')

const emit = defineEmits(['authenticated', 'announce'])

function setMode(nextMode) {
  if (mode.value === nextMode) return
  transitionName.value = mode.value === 'login' && nextMode === 'register'
    ? 'auth-forward'
    : mode.value === 'register' && nextMode === 'login'
      ? 'auth-back'
      : 'auth-switch'
  mode.value = nextMode
}

function submitAccess() {
  if (mode.value === 'register' && password.value !== passwordConfirm.value) {
    emit('announce', 'Passwords do not match.')
    return
  }

  if (mode.value !== 'admin') {
    otp.value = ''
    otpError.value = ''
    transitionName.value = 'auth-forward'
    stage.value = 'otp'
    emit('announce', `Demo verification code sent to ${email.value.trim()}.`)
    return
  }

  completeAccess()
}

function normalizeOtp(event) {
  otp.value = event.target.value.replace(/\D/g, '').slice(0, 6)
}

function verifyOtp() {
  if (otp.value !== '123456') {
    otpError.value = 'That code is not correct. Use the demo code shown below.'
    return
  }

  completeAccess()
}

function completeAccess() {
  emit('authenticated', {
    view: mode.value === 'admin' ? 'official' : 'user',
    profile: mode.value === 'register'
      ? { name: name.value.trim(), email: email.value.trim(), phone: phone.value.trim(), vehicle: '' }
      : null,
  })
  emit('announce', mode.value === 'register' ? 'Demo account created.' : 'Demo sign-in complete.')
}

function returnToCredentials() {
  transitionName.value = 'auth-back'
  stage.value = 'credentials'
}

function resendOtp() {
  otp.value = ''
  otpError.value = ''
  emit('announce', `Demo verification code resent to ${email.value.trim()}.`)
}
</script>

<template>
  <section class="page-content auth-page">
    <div class="auth-panel">
      <div v-if="stage === 'credentials'" class="auth-tabs" role="tablist" aria-label="Access type">
        <button :class="{ selected: mode === 'login' }" role="tab" :aria-selected="mode === 'login'" @click="setMode('login')">User login</button>
        <button :class="{ selected: mode === 'register' }" role="tab" :aria-selected="mode === 'register'" @click="setMode('register')">Register</button>
        <button :class="{ selected: mode === 'admin' }" role="tab" :aria-selected="mode === 'admin'" @click="setMode('admin')">Admin login</button>
      </div>

      <Transition :name="transitionName" mode="out-in">
        <div v-if="stage === 'credentials'" :key="`${mode}-credentials`" class="auth-content">
          <p class="eyebrow">PARKLINE · ACCESS</p>
          <h1>{{ mode === 'register' ? 'Create your account' : mode === 'admin' ? 'Admin sign in' : 'Welcome back' }}</h1>
          <p class="auth-intro">{{ mode === 'admin' ? 'Sign in to manage your parking site.' : mode === 'register' ? 'Set up your account to find and reserve parking.' : 'Sign in to find and manage parking.' }}</p>

          <form class="auth-form" @submit.prevent="submitAccess">
            <label v-if="mode === 'register'" for="register-name">Full name</label>
            <input v-if="mode === 'register'" id="register-name" v-model="name" autocomplete="name" placeholder="Your name" required />

            <label for="access-email">Email address</label>
            <input id="access-email" v-model="email" type="email" autocomplete="email" placeholder="name@example.com" required />

            <template v-if="mode === 'register'">
              <label for="register-phone">Phone number</label>
              <input id="register-phone" v-model="phone" type="tel" autocomplete="tel" placeholder="+91 98765 43210" required />
            </template>

            <label for="access-password">Password</label>
            <input id="access-password" v-model="password" type="password" :autocomplete="mode === 'register' ? 'new-password' : 'current-password'" minlength="8" placeholder="At least 8 characters" required />

            <template v-if="mode === 'register'">
              <label for="register-password-confirm">Confirm password</label>
              <input id="register-password-confirm" v-model="passwordConfirm" type="password" autocomplete="new-password" minlength="8" placeholder="Enter password again" required />
            </template>

            <button class="primary-button auth-submit" type="submit">{{ mode === 'register' ? 'Create account' : 'Sign in' }} <span>→</span></button>
          </form>
          <button v-if="mode === 'login'" class="auth-cta" type="button" @click="setMode('register')">New to Parkline? <span>Create an account</span></button>
          <button v-else-if="mode === 'register'" class="auth-cta" type="button" @click="setMode('login')">Already have an account? <span>Sign in</span></button>
          <p class="auth-note">Prototype only. Sign-in details are not sent to a server.</p>
        </div>
        <div v-else :key="`${mode}-otp`" class="auth-content">
          <button class="auth-back" type="button" @click="returnToCredentials"><span aria-hidden="true">←</span> Back</button>
          <p class="eyebrow">PARKLINE · VERIFY</p>
          <h1>Check your email</h1>
          <p class="auth-intro">Enter the 6-digit verification code sent to <strong>{{ email.trim() }}</strong>.</p>

          <form class="auth-form otp-form" @submit.prevent="verifyOtp">
            <label for="access-otp">Verification code</label>
            <input id="access-otp" :value="otp" type="text" inputmode="numeric" autocomplete="one-time-code" pattern="[0-9]{6}" maxlength="6" placeholder="000000" aria-describedby="otp-help otp-error" required @input="normalizeOtp" />
            <p id="otp-error" class="otp-error" role="alert">{{ otpError }}</p>
            <button class="primary-button auth-submit" type="submit">Verify and continue <span>→</span></button>
          </form>
          <p id="otp-help" class="auth-note otp-help">Demo code: <strong>123456</strong>. This prototype verifies locally only.</p>
          <button class="auth-cta" type="button" @click="resendOtp">Didn’t get a code? <span>Resend code</span></button>
        </div>
      </Transition>
    </div>
  </section>
</template>
