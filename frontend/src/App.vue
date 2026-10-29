<script setup>
import { ref } from 'vue'
import OfficialPage from './components/OfficialPage/OfficialPage.vue'
import UserPage from './components/UserPage/UserPage.vue'
import UserAccountPage from './components/UserAccountPage.vue'
import UserManagementPage from './components/UserManagementPage.vue'
import AuthPage from './components/AuthPage.vue'
import NavigationBar from './components/NavigationBar.vue'

const darkMode = ref(false)
const notice = ref('')
const profile = ref({ name: 'Arjun Shah', email: 'arjun@example.com', phone: '+91 98765 43210', vehicle: 'KA 05 MJ 2041' })

function announce(message) {
  notice.value = message
  window.setTimeout(() => (notice.value = ''), 2200)
}

function saveProfile(updatedProfile) {
  profile.value = updatedProfile
  announce('Account changes saved.')
}

function completeAccess(result, navigate) {
  if (result.profile) profile.value = { ...profile.value, ...result.profile }
  navigate(result.view)
}

</script>

<template>
  <div :class="['app-shell', { 'dark-mode': darkMode }]">
    <NavigationBar :profile="profile" @announce="announce" @theme-change="darkMode = $event">
      <template #default="{ view: activeView, navigate }">

        <Transition name="page" mode="out-in" appear>
          <OfficialPage v-if="activeView === 'official'" :profile="profile" @announce="announce" />
          <UserManagementPage v-else-if="activeView === 'users'" @announce="announce" />
          <UserPage v-else-if="activeView === 'user'" @announce="announce" />
          <UserAccountPage v-else-if="activeView === 'account'" :profile="profile" @save="saveProfile" @cancel="navigate('user')" />
          <AuthPage v-else :profile="profile" @authenticated="(result) => completeAccess(result, navigate)" @announce="announce" />
        </Transition>

        <!-- Legacy page markup removed from the rendered shell. -->
        <!--
      <section v-if="false && view === 'official'" class="page-content">
        <div class="page-heading"><div><p class="eyebrow">Thursday, 24 August 2026 <span class="live-pill"><i></i> Live</span></p><h1>Good morning, Arjun</h1><p class="subheading">Here’s what’s happening at your parking site today.</p></div><button class="primary-button" @click="announce('Invite link copied to clipboard.')"><span>＋</span> Add a vehicle</button></div>

        <div class="alert-banner" :class="{ closed: !gateOpen }"><div class="alert-icon">{{ gateOpen ? '↗' : '!' }}</div><div><b>Gate is {{ gateOpen ? 'open' : 'closed' }}</b><span>{{ gateOpen ? 'Vehicles can enter and exit the premises.' : 'Entry is paused until the gate is opened.' }}</span></div><button @click="gateOpen = !gateOpen">{{ gateOpen ? 'Close gate' : 'Open gate' }}</button></div>

        <div class="stats-grid"><article class="stat-card primary-stat"><div class="stat-top"><span>Available spaces</span><span class="stat-icon">⌗</span></div><strong>{{ availableCount }}<small> / {{ slots.length }}</small></strong><div class="progress"><span :style="{ width: `${(availableCount / slots.length) * 100}%` }"></span></div><p><b>{{ occupancyRate }}%</b> occupancy across all spaces</p></article><article class="stat-card"><div class="stat-top"><span>Today’s entries</span><span class="stat-icon muted">↘</span></div><strong>128</strong><p class="trend up">↑ 12.4% <em>vs. yesterday</em></p></article><article class="stat-card"><div class="stat-top"><span>Today’s exits</span><span class="stat-icon muted">↗</span></div><strong>96</strong><p class="trend down">↓ 4.8% <em>vs. yesterday</em></p></article><article class="stat-card"><div class="stat-top"><span>Working schedule</span><span class="stat-icon muted">◷</span></div><strong>{{ isWorkingDay ? 'Open' : 'Closed' }}</strong><p class="schedule-line"><span :class="['schedule-dot', { off: !isWorkingDay }]" /> {{ isWorkingDay ? '08:00 – 22:00 today' : 'Rest day today' }}</p></article></div>

        <div class="workspace-grid"><section class="panel layout-panel"><div class="panel-heading"><div><h2>Parking layout</h2><p>Tap a space to update its status.</p></div><div class="floor-tabs"><button :class="{ selected: selectedFloor === 'Ground floor' }" @click="selectedFloor = 'Ground floor'">Ground floor</button><button :class="{ selected: selectedFloor === 'Basement' }" @click="selectedFloor = 'Basement'">Basement</button></div></div><div class="layout-meta"><span><i class="legend available"></i> Available <b>{{ availableCount }}</b></span><span><i class="legend occupied"></i> Occupied <b>3</b></span><span><i class="legend reserved"></i> Reserved <b>2</b></span><span><i class="legend maintenance"></i> Maintenance <b>1</b></span></div><div class="parking-map"><div class="driveway"><span>ENTRY</span><div class="arrow">↓</div><small>ONE WAY</small></div><div v-for="slot in slots" :key="slot.id" :class="['parking-slot', slot.status]" @click="toggleSlot(slot)"><span class="slot-id">{{ slot.id }}</span><span class="slot-type">{{ slot.type }}</span><span v-if="slot.status === 'occupied'" class="plate">{{ slot.plate }}</span><span v-else-if="slot.status === 'maintenance'" class="slot-note">Offline</span><span v-else class="slot-status">{{ slot.status }}</span></div><div class="exit-lane"><span>EXIT</span><div class="arrow">↑</div></div></div></section><aside class="side-stack"><section class="panel schedule-panel"><div class="panel-heading"><div><h2>Site schedule</h2><p>Control when your site is operational.</p></div><button class="mini-button" @click="isWorkingDay = !isWorkingDay">Edit</button></div><div class="schedule-toggle"><div><b>Today’s schedule</b><small>{{ isWorkingDay ? 'Working day' : 'Rest day' }}</small></div><button :class="['toggle', { on: isWorkingDay }]" @click="isWorkingDay = !isWorkingDay"><span></span></button></div><div class="hours"><span>Opening hours</span><b>{{ isWorkingDay ? '08:00 – 22:00' : '—' }}</b></div></section><section class="panel activity-panel"><div class="panel-heading"><div><h2>Recent activity</h2><p>Latest events from your site.</p></div><button class="mini-button" @click="announce('Showing all activity.')">View all</button></div><div class="activity-item"><span class="activity-icon green">↗</span><div><b>Gate opened</b><small>by Arjun Shah · 12 min ago</small></div></div><div class="activity-item"><span class="activity-icon blue">⌗</span><div><b>Vehicle entered</b><small>KA 05 MJ 2041 · 18 min ago</small></div></div><div class="activity-item"><span class="activity-icon orange">◷</span><div><b>Space A-04 reserved</b><small>Accessible space · 31 min ago</small></div></div></section></aside></div>
      </section>

      <section v-if="false" class="page-content user-view"><div class="user-hero"><p class="eyebrow">PARKLINE · INDIRANAGAR</p><h1>Find your space.</h1><p>Real-time parking availability, without the circling.</p><div class="search-row"><input placeholder="Enter vehicle number" /><button class="primary-button" @click="announce(`${availableCount} spaces are available right now.`)">Find parking <span>→</span></button></div></div><div class="user-summary"><div><span class="live-pill"><i></i> Live availability</span><h2>{{ availableCount }} spaces available</h2><p>Updated just now · Open until 10:00 PM</p></div><div class="user-rate"><strong>{{ occupancyRate }}%</strong><span>occupied</span></div></div><div class="user-slots"><div v-for="slot in slots.filter((slot) => slot.status === 'available')" :key="slot.id" class="user-slot"><span>{{ slot.id }}</span><b>{{ slot.type }}</b><button @click="announce(`${slot.id} selected. Please proceed to the gate.`)">Select <span>→</span></button></div></div></section>
        -->
      </template>
    </NavigationBar>
    <div v-if="notice" class="toast">{{ notice }}</div>
  </div>
</template>

