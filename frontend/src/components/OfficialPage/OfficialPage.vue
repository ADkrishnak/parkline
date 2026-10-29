<script setup>
import { computed, ref } from 'vue'
import ParkingLayout from './ParkingLayout.vue'
import ParkingCameraImage from './ParkingCameraImage.vue'
import AdminAnalytics from './AdminAnalytics.vue'

const gateOpen = ref(true)
const isWorkingDay = ref(true)
const selectedFloor = ref('Ground floor')
const selectedCamera = ref('Gate camera')
const cameras = ['Gate camera', 'Parking aisle', 'Exit camera']
const slots = ref([
  { id: 'A-01', status: 'occupied', type: 'EV', plate: 'KA 05 MJ 2041' },
  { id: 'A-02', status: 'available', type: 'Regular' },
  { id: 'A-03', status: 'available', type: 'Regular' },
  { id: 'A-04', status: 'reserved', type: 'Accessible' },
  { id: 'A-05', status: 'occupied', type: 'Regular', plate: 'KA 03 NK 1188' },
  { id: 'A-06', status: 'available', type: 'Regular' },
  { id: 'B-01', status: 'available', type: 'Regular' },
  { id: 'B-02', status: 'occupied', type: 'Regular', plate: 'KA 01 AE 4420' },
  { id: 'B-03', status: 'available', type: 'Regular' },
  { id: 'B-04', status: 'maintenance', type: 'Regular' },
  { id: 'B-05', status: 'available', type: 'Regular' },
  { id: 'B-06', status: 'reserved', type: 'Accessible' },
])
const availableCount = computed(() => slots.value.filter((slot) => slot.status === 'available').length)
const occupancyRate = computed(() => Math.round(((slots.value.length - availableCount.value) / slots.value.length) * 100))
const { profile } = defineProps({
  profile: {
    type: Object,
    required: true,
  },
})
const emit = defineEmits(['announce'])

function toggleSlot(slot) {
  if (slot.status !== 'maintenance') {
    slot.status = slot.status === 'available' ? 'occupied' : 'available'
    emit('announce', `${slot.id} marked ${slot.status}.`)
  }
}

function selectCamera(camera) {
  selectedCamera.value = camera
  emit('announce', `${camera} selected.`)
}
</script>

<template>
  <section class="page-content">
    <div class="page-heading"><div><p class="eyebrow">Thursday, 24 August 2026 <span class="live-pill"><i></i> Live</span></p><h1>Good morning, {{ profile.name.split(' ')[0] }}</h1><p class="subheading">Here’s what’s happening at your parking site today.</p></div><button class="primary-button" @click="emit('announce', 'Invite link copied to clipboard.')"><span>＋</span> Add a vehicle</button></div>
    <div class="alert-banner" :class="{ closed: !gateOpen }"><div class="alert-icon">{{ gateOpen ? '↗' : '!' }}</div><div><b>Gate is {{ gateOpen ? 'open' : 'closed' }}</b><span>{{ gateOpen ? 'Vehicles can enter and exit the premises.' : 'Entry is paused until the gate is opened.' }}</span></div><button @click="gateOpen = !gateOpen">{{ gateOpen ? 'Close gate' : 'Open gate' }}</button></div>
    <div class="stats-grid"><article class="stat-card primary-stat"><div class="stat-top"><span>Available spaces</span><span class="stat-icon">⌗</span></div><strong>{{ availableCount }}<small> / {{ slots.length }}</small></strong><div class="progress"><span :style="{ width: `${(availableCount / slots.length) * 100}%` }"></span></div><p><b>{{ occupancyRate }}%</b> occupancy across all spaces</p></article><article class="stat-card"><div class="stat-top"><span>Today’s entries</span><span class="stat-icon muted">↘</span></div><strong>128</strong><p class="trend up">↑ 12.4% <em>vs. yesterday</em></p></article><article class="stat-card"><div class="stat-top"><span>Today’s exits</span><span class="stat-icon muted">↗</span></div><strong>96</strong><p class="trend down">↓ 4.8% <em>vs. yesterday</em></p></article><article class="stat-card"><div class="stat-top"><span>Working schedule</span><span class="stat-icon muted">◷</span></div><strong>{{ isWorkingDay ? 'Open' : 'Closed' }}</strong><p class="schedule-line"><span :class="['schedule-dot', { off: !isWorkingDay }]" /> {{ isWorkingDay ? '08:00 – 22:00 today' : 'Rest day today' }}</p></article></div>
    <div class="workspace-grid"><section class="panel layout-panel"><div class="panel-heading"><div><h2>Parking layout</h2><p>Tap a space to update its status.</p></div><div class="floor-tabs"><button :class="{ selected: selectedFloor === 'Ground floor' }" @click="selectedFloor = 'Ground floor'">Ground floor</button><button :class="{ selected: selectedFloor === 'Basement' }" @click="selectedFloor = 'Basement'">Basement</button></div></div><div class="layout-meta"><span><i class="legend available"></i> Available <b>{{ availableCount }}</b></span><span><i class="legend occupied"></i> Occupied <b>3</b></span><span><i class="legend reserved"></i> Reserved <b>2</b></span><span><i class="legend maintenance"></i> Maintenance <b>1</b></span></div><div class="parking-map"><div class="driveway"><span>ENTRY</span><div class="arrow">↓</div><small>ONE WAY</small></div><div v-for="slot in slots" :key="slot.id" :class="['parking-slot', slot.status]" @click="toggleSlot(slot)"><span class="slot-id">{{ slot.id }}</span><span class="slot-type">{{ slot.type }}</span><span v-if="slot.status === 'occupied'" class="plate">{{ slot.plate }}</span><span v-else-if="slot.status === 'maintenance'" class="slot-note">Offline</span><span v-else class="slot-status">{{ slot.status }}</span></div><div class="exit-lane"><span>EXIT</span><div class="arrow">↑</div></div></div></section><aside class="side-stack"><section class="panel schedule-panel"><div class="panel-heading"><div><h2>Site schedule</h2><p>Control when your site is operational.</p></div><button class="mini-button" @click="isWorkingDay = !isWorkingDay">Edit</button></div><div class="schedule-toggle"><div><b>Today’s schedule</b><small>{{ isWorkingDay ? 'Working day' : 'Rest day' }}</small></div><button :class="['toggle', { on: isWorkingDay }]" @click="isWorkingDay = !isWorkingDay"><span></span></button></div><div class="hours"><span>Opening hours</span><b>{{ isWorkingDay ? '08:00 – 22:00' : '—' }}</b></div></section><section class="panel activity-panel"><div class="panel-heading"><div><h2>Recent activity</h2><p>Latest events from your site.</p></div><button class="mini-button" @click="emit('announce', 'Showing all activity.')">View all</button></div><div class="activity-item"><span class="activity-icon green">↗</span><div><b>Gate opened</b><small>by {{ profile.name }} · 12 min ago</small></div></div><div class="activity-item"><span class="activity-icon blue">⌗</span><div><b>Vehicle entered</b><small>KA 05 MJ 2041 · 18 min ago</small></div></div><div class="activity-item"><span class="activity-icon orange">◷</span><div><b>Space A-04 reserved</b><small>Accessible space · 31 min ago</small></div></div></section></aside></div>
    <section class="panel cctv-panel"><div class="panel-heading"><div><h2>CCTV footage</h2><p>Monitor your site in real time.</p></div><span class="camera-live"><i></i> Recording</span></div><div class="cctv-layout"><div class="cctv-feed"><div class="feed-overlay"><span class="feed-label">{{ selectedCamera.toUpperCase() }}</span><span>24 AUG 2026 · 09:42:18</span></div><ParkingCameraImage /><div class="feed-controls"><span><i></i> LIVE</span><button @click="emit('announce', 'CCTV feed fullscreen is unavailable in demo mode.')">⛶</button></div></div><div class="camera-list"><p class="camera-list-title">Cameras <b>{{ cameras.length }}</b></p><button v-for="camera in cameras" :key="camera" :class="['camera-item', { selected: selectedCamera === camera }]" @click="selectCamera(camera)"><span class="camera-thumb"><i></i></span><span><b>{{ camera }}</b><small>{{ camera === 'Parking aisle' ? '12 spaces in view' : 'Online · 1080p' }}</small></span><strong>›</strong></button></div></div></section>
    <AdminAnalytics />
    <div class="parking-layout-slot"><ParkingLayout v-model:floor="selectedFloor" :slots="slots" :available-count="availableCount" @toggle-slot="toggleSlot" /></div>
  </section>
</template>
