<script setup>
import { computed, ref } from 'vue'
import LocationMapPanel from '../LocationMapPanel.vue'

const slots = ref([
  { id: 'A-02', status: 'available', type: 'Regular' },
  { id: 'A-03', status: 'available', type: 'Regular' },
  { id: 'A-06', status: 'available', type: 'Regular' },
  { id: 'B-01', status: 'available', type: 'Regular' },
  { id: 'B-03', status: 'available', type: 'Regular' },
  { id: 'B-05', status: 'available', type: 'Regular' },
])
const availableCount = computed(() => slots.value.length)
const occupancyRate = ref(50)
const selectedSlot = ref('')
const emit = defineEmits(['announce'])

function selectSlot(slot) {
  selectedSlot.value = slot.id
  emit('announce', `${slot.id} selected. Please proceed to the gate.`)
}

</script>

<template>
  <section class="page-content user-view"><div class="user-hero"><p class="eyebrow">PARKLINE · ADYAR, MANGALURU</p><h1>Find your space.</h1><p>Real-time parking availability, without the circling.</p><div class="search-row"><input placeholder="Enter vehicle number" /><button class="primary-button" @click="emit('announce', `${availableCount} spaces are available right now.`)">Find parking <span>→</span></button></div></div><LocationMapPanel @announce="emit('announce', $event)" /><section class="panel user-map-panel"><div class="panel-heading"><div><h2>Choose a space</h2><p>Tap an available space on the map.</p></div><span class="map-key"><i></i> Available</span></div><div class="user-parking-map"><div class="user-entry"><span>ENTRY</span><b>↓</b></div><button v-for="slot in slots" :key="slot.id" :class="['user-map-slot', { selected: selectedSlot === slot.id }]" @click="selectSlot(slot)"><strong>{{ slot.id }}</strong><small>{{ slot.type }}</small></button><div class="user-exit"><span>EXIT</span><b>↑</b></div></div><div v-if="selectedSlot" class="selected-space"><span><i></i> Selected space</span><b>{{ selectedSlot }}</b><button @click="emit('announce', `${selectedSlot} confirmed. Please proceed to the gate.`)">Confirm space <span>→</span></button></div></section><div class="user-slots"><div v-for="slot in slots" :key="slot.id" :class="['user-slot', { selected: selectedSlot === slot.id }]" @click="selectSlot(slot)"><span>{{ slot.id }}</span><b>{{ slot.type }}</b><button @click.stop="selectSlot(slot)">Select <span>→</span></button></div></div></section>
</template>
