<script setup>
const props = defineProps({
  slots: { type: Array, required: true },
  availableCount: { type: Number, required: true },
})

const selectedFloor = defineModel('floor', { default: 'Ground floor' })
const emit = defineEmits(['toggle-slot'])
</script>

<template>
  <section id="parking-layout" class="panel layout-panel parking-layout-panel">
    <div class="panel-heading">
      <div><h2>Parking layout</h2><p>Tap a space to update its status.</p></div>
      <div class="floor-tabs">
        <button :class="{ selected: selectedFloor === 'Ground floor' }" @click="selectedFloor = 'Ground floor'">Ground floor</button>
        <button :class="{ selected: selectedFloor === 'Basement' }" @click="selectedFloor = 'Basement'">Basement</button>
      </div>
    </div>
    <div class="layout-meta">
      <span><i class="legend available"></i> Available <b>{{ props.availableCount }}</b></span>
      <span><i class="legend occupied"></i> Occupied <b>3</b></span>
      <span><i class="legend reserved"></i> Reserved <b>2</b></span>
      <span><i class="legend maintenance"></i> Maintenance <b>1</b></span>
    </div>
    <div class="parking-map">
      <div class="driveway"><span>ENTRY</span><div class="arrow">↓</div><small>ONE WAY</small></div>
      <div v-for="slot in props.slots" :key="slot.id" :class="['parking-slot', slot.status]" @click="emit('toggle-slot', slot)">
        <span class="slot-id">{{ slot.id }}</span><span class="slot-type">{{ slot.type }}</span>
        <span v-if="slot.status === 'occupied'" class="plate">{{ slot.plate }}</span>
        <span v-else-if="slot.status === 'maintenance'" class="slot-note">Offline</span>
        <span v-else class="slot-status">{{ slot.status }}</span>
      </div>
      <div class="exit-lane"><span>EXIT</span><div class="arrow">↑</div></div>
    </div>
  </section>
</template>
