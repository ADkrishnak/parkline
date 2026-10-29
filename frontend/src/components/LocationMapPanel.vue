<script setup>
import { computed, ref } from 'vue'

const siteAddress = 'Adyar, Mangaluru'
const mapQuery = ref('12.867297,74.925430')
const mapUrl = computed(() => `https://www.google.com/maps?q=${encodeURIComponent(mapQuery.value)}&z=17&output=embed`)
const locationMode = ref('map')

function openDirections() {
  window.open('https://maps.app.goo.gl/jd7zvhz396W9pkEo8', '_blank', 'noopener')
}

function searchMap() {
  if (!mapQuery.value.trim()) return
  emit('announce', `Searching map for ${mapQuery.value.trim()}.`)
}

const emit = defineEmits(['announce'])
</script>

<template>
  <section class="panel location-map-panel">
    <div class="panel-heading">
      <div>
        <h2>Parking location</h2>
        <p>{{ siteAddress }}</p>
      </div>
      <div class="location-actions">
        <div class="location-tabs">
          <button :class="{ selected: locationMode === 'street' }" @click="locationMode = 'street'">Street View</button>
          <button :class="{ selected: locationMode === 'map' }" @click="locationMode = 'map'">Map</button>
        </div>
        <button class="mini-button" @click="openDirections">Directions <span>↗</span></button>
      </div>
    </div>
    <div class="location-map" :class="{ 'street-view': locationMode === 'street' }">
      <iframe
        v-if="locationMode === 'street'"
        title="Parkline Adyar, Mangaluru Street View"
        src="https://www.google.com/maps?q=12.867297%2C74.925430&layer=c&cbll=12.867297,74.925430&cbp=12,90,0,0,5&output=svembed"
        loading="lazy"
      ></iframe>
      <iframe v-else class="real-map" title="Real map of Parkline Adyar, Mangaluru" :src="mapUrl" loading="lazy"></iframe>
      <div class="street-overlay">
        <span class="street-compass">N</span>
        <div>
          <b>{{ locationMode === 'street' ? 'Street View' : 'Map view' }}</b>
          <small>{{ locationMode === 'street' ? 'Adyar entrance · Mangaluru' : 'Google Maps · Mangaluru' }}</small>
        </div>
      </div>
      <div class="map-pin"><span>P</span><b>Parkline</b><small>Parking entrance</small></div>
    </div>
    <form class="map-search-fallback" @submit.prevent="searchMap">
      <label for="map-place">Search map location</label>
      <input id="map-place" v-model="mapQuery" placeholder="Search a place" />
      <button type="submit">Search</button>
    </form>
  </section>
</template>