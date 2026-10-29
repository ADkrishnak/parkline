<script setup>
import { computed, ref } from 'vue'
import ParkingCameraImage from './OfficialPage/ParkingCameraImage.vue'

const emit = defineEmits(['announce'])
const search = ref('')
const statusFilter = ref('all')
const users = ref([
  { id: 1, name: 'Manish Verma', email: 'manish.verma@example.com', vehicle: 'KA 03 MN 2814', lastVisit: 'Today, 9:18 AM', active: true },
  { id: 2, name: 'Caron Vas', email: 'caron.vas@example.com', vehicle: 'KA 05 RH 0926', lastVisit: 'Today, 8:42 AM', active: true },
  { id: 3, name: 'Anika Rao', email: 'anika.rao@example.com', vehicle: 'KA 01 AR 7650', lastVisit: 'Yesterday', active: true },
  { id: 4, name: 'Dev Malhotra', email: 'dev.malhotra@example.com', vehicle: 'KA 03 DM 4412', lastVisit: 'Yesterday', active: false },
  { id: 5, name: 'Sara Joseph', email: 'sara.joseph@example.com', vehicle: 'KA 05 SJ 6183', lastVisit: '18 Aug 2026', active: true },
  { id: 6, name: 'Kabir Das', email: 'kabir.das@example.com', vehicle: 'KA 01 KD 3048', lastVisit: '16 Aug 2026', active: false },
])

const activeCount = computed(() => users.value.filter((user) => user.active).length)
const filteredUsers = computed(() => {
  const term = search.value.trim().toLowerCase()
  return users.value.filter((user) => {
    const matchesSearch = !term || [user.name, user.email, user.vehicle].some((value) => value.toLowerCase().includes(term))
    const matchesStatus = statusFilter.value === 'all' || (statusFilter.value === 'active' ? user.active : !user.active)
    return matchesSearch && matchesStatus
  })
})

function toggleAccess(user) {
  user.active = !user.active
  emit('announce', `${user.name}'s access ${user.active ? 'restored' : 'suspended'}.`)
}
</script>

<template>
  <section class="page-content users-page">
    <div class="page-heading">
      <div>
        <p class="eyebrow">PARKLINE · INDIRANAGAR</p>
        <h1>Users</h1>
        <p class="subheading">Manage parking access for people at this site.</p>
      </div>
      <button class="primary-button" type="button" @click="emit('announce', 'User invitation link copied.')"><span>＋</span> Invite user</button>
    </div>

    <div class="users-overview">
      <div><span>Registered users</span><strong>{{ users.length }}</strong></div>
      <div><span>Active access</span><strong>{{ activeCount }}</strong></div>
      <div><span>Suspended</span><strong>{{ users.length - activeCount }}</strong></div>
    </div>

    <section class="users-site-preview panel" aria-label="Parking site preview">
      <ParkingCameraImage compact />
      <div class="users-site-copy">
        <p class="eyebrow">PARKLINE · LIVE SITE</p>
        <h2>Parking area A</h2>
        <p>Indiranagar · 12 spaces monitored</p>
      </div>
      <span class="users-site-status"><i></i> Camera online</span>
    </section>

    <section class="panel users-panel" aria-label="User directory">
      <div class="users-toolbar">
        <div>
          <h2>User directory</h2>
          <p>{{ filteredUsers.length }} of {{ users.length }} users</p>
        </div>
        <div class="users-filters">
          <label class="users-search">
            <span aria-hidden="true">⌕</span>
            <input v-model="search" type="search" placeholder="Search name, email or vehicle" aria-label="Search users" />
          </label>
          <select v-model="statusFilter" aria-label="Filter users by access status">
            <option value="all">All access</option>
            <option value="active">Active</option>
            <option value="suspended">Suspended</option>
          </select>
        </div>
      </div>

      <div class="users-table-wrap">
        <table class="users-table">
          <thead>
            <tr><th scope="col">User</th><th scope="col">Vehicle</th><th scope="col">Last visit</th><th scope="col">Access</th><th scope="col"><span class="visually-hidden">Actions</span></th></tr>
          </thead>
          <tbody>
            <tr v-for="user in filteredUsers" :key="user.id">
              <td data-label="User"><div class="user-identity"><span class="user-avatar">{{ user.name.split(' ').map((part) => part[0]).slice(0, 2).join('').toUpperCase() }}</span><span><b>{{ user.name }}</b><small>{{ user.email }}</small></span></div></td>
              <td data-label="Vehicle">{{ user.vehicle }}</td>
              <td data-label="Last visit">{{ user.lastVisit }}</td>
              <td data-label="Access"><span :class="['user-access-status', { suspended: !user.active }]">{{ user.active ? 'Active' : 'Suspended' }}</span></td>
              <td class="user-action-cell"><button class="user-access-action" type="button" :aria-label="`${user.active ? 'Suspend' : 'Restore'} access for ${user.name}`" @click="toggleAccess(user)">{{ user.active ? 'Suspend' : 'Restore' }}</button></td>
            </tr>
            <tr v-if="filteredUsers.length === 0"><td class="users-empty" colspan="5">No users match your search.</td></tr>
          </tbody>
        </table>
      </div>
    </section>
  </section>
</template>
