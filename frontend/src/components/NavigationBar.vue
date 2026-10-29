<script setup>
import { computed, nextTick, ref } from 'vue'

const view = ref('auth')
const darkMode = ref(false)
const navOpen = ref(false)
const profileMenuOpen = ref(false)
const touchStart = ref({ x: 0, y: 0 })
const navDrag = ref(0)
const navDragging = ref(false)

const { profile } = defineProps({
  profile: {
    type: Object,
    default: () => ({ name: 'Arjun Shah', email: 'arjun@example.com' }),
  },
})

const emit = defineEmits(['view-change', 'announce', 'theme-change'])

const navStyle = computed(() => {
  if (!navDragging.value) return undefined
  return { transform: navOpen.value ? `translateX(${navDrag.value}px)` : `translateX(calc(-100% + ${navDrag.value}px))` }
})

function selectView(nextView) {
  view.value = nextView
  navOpen.value = false
  profileMenuOpen.value = false
  emit('view-change', nextView)
  if (nextView === 'official') {
    document.documentElement.scrollTop = 0
    document.body.scrollTop = 0
  }
}

async function goToParkingLayout() {
  selectView('official')
  await nextTick()
  document.getElementById('parking-layout')?.scrollIntoView({ behavior: 'smooth', block: 'start' })
}

function toggleTheme() {
  darkMode.value = !darkMode.value
  emit('theme-change', darkMode.value)
}

function startSwipe(event) {
  const startX = event.touches[0].clientX
  if (!navOpen.value && startX > 36) return
  touchStart.value = { x: startX, y: event.touches[0].clientY }
  navDragging.value = true
  navDrag.value = 0
}

function moveSwipe(event) {
  if (!navDragging.value) return
  const distanceX = event.touches[0].clientX - touchStart.value.x
  const distanceY = Math.abs(event.touches[0].clientY - touchStart.value.y)
  if (distanceY > 70) return
  navDrag.value = navOpen.value ? Math.min(0, Math.max(-280, distanceX)) : Math.min(280, Math.max(0, distanceX))
}

function finishSwipe(event, direction) {
  if (!navDragging.value) return
  const distanceX = event.changedTouches[0].clientX - touchStart.value.x
  const distanceY = Math.abs(event.changedTouches[0].clientY - touchStart.value.y)
  if (distanceY <= 70 && Math.abs(distanceX) >= 55) {
    if (direction === 'edge' && distanceX > 0) navOpen.value = true
    if (direction === 'drawer' && distanceX < 0) navOpen.value = false
  }
  navDragging.value = false
  navDrag.value = 0
}

function announce(message) {
  emit('announce', message)
}
</script>

<template>
  <aside v-if="view !== 'auth'" :class="['sidebar', { 'nav-open': navOpen, 'is-dragging': navDragging }]" :style="navStyle" @touchstart="startSwipe" @touchmove="moveSwipe" @touchend="finishSwipe($event, 'drawer')">
    <div class="brand"><span class="brand-mark">P</span><span>parkline</span></div>
    <div class="site-switcher"><span class="site-dot"></span><span><b>Indiranagar</b><small>BLR-04 · Bengaluru</small></span><span class="chevron">⌄</span></div>
    <nav>
      <template v-if="view === 'official' || view === 'users'">
        <p class="nav-label">Workspace</p>
        <button :class="['nav-item', { active: view === 'official' }]" @click="selectView('official')"><span>▦</span>Overview</button>
        <button class="nav-item" @click="goToParkingLayout"><span>⌗</span>Parking layout</button>
        <button class="nav-item" @click="announce('Activity history is coming next.')"><span>◷</span>Activity</button>
        <p class="nav-label secondary-label">Manage</p>
        <button :class="['nav-item', { active: view === 'users' }]" @click="selectView('users')"><span>♙</span>Users</button>
        <button class="nav-item" @click="announce('Team management opened.')"><span>♙</span>Team</button>
      </template>
      <template v-else-if="view === 'user' || view === 'account'">
        <p v-if="view === 'account'" class="nav-label">Account</p>
        <button v-if="view === 'account'" class="nav-item" @click="selectView('user')"><span>⌂</span>Find a spot</button>
      </template>
      <template v-else>
        <p class="nav-label">Access</p>
        <button class="nav-item active"><span>↪</span>Sign in or register</button>
      </template>
    </nav>
    <div class="sidebar-footer"><div class="avatar">{{ profile.name.split(' ').map((part) => part[0]).slice(0, 2).join('').toUpperCase() }}</div><div><b>{{ profile.name }}</b><small>{{ view === 'official' || view === 'users' ? 'Site manager' : 'Parking user' }}</small></div><div class="profile-menu-wrap"><button class="more" aria-label="Profile options" :aria-expanded="profileMenuOpen" @click="profileMenuOpen = !profileMenuOpen">•••</button><div v-if="profileMenuOpen" class="profile-menu"><button v-if="view === 'user' || view === 'account'" @click="selectView('account')">Edit account</button><button @click="announce('Settings opened.'); profileMenuOpen = false">Settings</button><button class="profile-menu-theme" :aria-pressed="darkMode" @click="toggleTheme"><span>Dark mode</span><span :class="['theme-toggle', { on: darkMode }]"><span></span></span></button><button @click="selectView('auth'); announce('You have been logged out.')">Log out</button></div></div></div>
  </aside>
  <main class="main-content" @touchstart="startSwipe" @touchmove="moveSwipe" @touchend="finishSwipe($event, 'edge')">
    <header class="topbar"><button v-if="view !== 'auth'" class="menu-button" :class="{ open: navOpen }" aria-label="Toggle navigation" :aria-expanded="navOpen" @click="navOpen = !navOpen"><span></span><span></span><span></span></button><div :class="['mobile-brand', { 'auth-brand': view === 'auth' }]"><span class="brand-mark">P</span> parkline</div><div v-if="view !== 'auth'" class="breadcrumb">Operations <span>/</span> <b>{{ view === 'official' ? 'Overview' : view === 'users' ? 'Users' : view === 'user' ? 'Find a spot' : 'Edit account' }}</b></div><div v-if="view !== 'auth'" class="top-actions"><button class="icon-button" aria-label="Notifications">♧<i></i></button><button class="help-button" @click="announce('Support is online.')">? <span>Help center</span></button></div></header>
    <slot :view="view" :navigate="selectView" />
  </main>
  <button v-if="navOpen && view !== 'auth'" class="nav-backdrop" aria-label="Close navigation" @click="navOpen = false"></button>
</template>
