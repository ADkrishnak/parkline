<script setup>
import { ref } from 'vue'

const props = defineProps({
  profile: {
    type: Object,
    required: true,
  },
})

const emit = defineEmits(['save', 'cancel'])
const name = ref(props.profile.name)
const email = ref(props.profile.email)
const phone = ref(props.profile.phone)
const vehicle = ref(props.profile.vehicle)

function saveAccount() {
  emit('save', {
    name: name.value.trim(),
    email: email.value.trim(),
    phone: phone.value.trim(),
    vehicle: vehicle.value.trim(),
  })
}
</script>

<template>
  <section class="page-content account-page">
    <div class="page-heading">
      <div>
        <button class="account-back" type="button" @click="emit('cancel')"><span aria-hidden="true">←</span> Back</button>
        <p class="eyebrow">ACCOUNT · PROFILE</p>
        <h1>Edit your account</h1>
        <p class="subheading">Update your contact details and vehicle information.</p>
      </div>
    </div>

    <form class="panel account-form" @submit.prevent="saveAccount">
      <div class="account-form-heading">
        <div class="avatar account-avatar">{{ name.split(' ').map((part) => part[0]).slice(0, 2).join('').toUpperCase() || 'U' }}</div>
        <div><h2>Personal details</h2><p>Changes apply to this demo session.</p></div>
      </div>
      <div class="account-fields">
        <label for="account-name">Full name</label>
        <input id="account-name" v-model="name" autocomplete="name" required />
        <label for="account-email">Email address</label>
        <input id="account-email" v-model="email" type="email" autocomplete="email" required />
        <label for="account-phone">Phone number</label>
        <input id="account-phone" v-model="phone" type="tel" autocomplete="tel" required />
        <label for="account-vehicle">Vehicle registration</label>
        <input id="account-vehicle" v-model="vehicle" autocomplete="off" required />
      </div>
      <div class="account-actions">
        <button class="mini-button" type="button" @click="emit('cancel')">Cancel</button>
        <button class="primary-button" type="submit">Save changes <span>→</span></button>
      </div>
    </form>
  </section>
</template>
