<script setup>
defineProps({
  compact: {
    type: Boolean,
    default: false,
  },
})

const vehicles = [
  { x: 78, color: '#d9e3df' },
  { x: 216, color: '#c95f50' },
  { x: 354, color: '#e8ece8' },
  { x: 492, color: '#496d91' },
  { x: 630, color: '#d6a64c' },
  { x: 768, color: '#dfe5df' },
]
</script>

<template>
  <div :class="['parking-camera-image', { compact }]">
    <svg viewBox="0 0 960 420" role="img" aria-label="Overhead view of cars parked in a marked parking area">
      <rect width="960" height="420" fill="#43534b" />
      <path d="M0 0h960v54H0z" fill="#617b68" />
      <path d="M0 54h960v18H0z" fill="#899b84" opacity=".55" />
      <path d="M0 356h960v64H0z" fill="#34433c" />
      <path d="M0 372h960" stroke="#c8d0b5" stroke-dasharray="20 18" stroke-width="3" opacity=".65" />
      <g fill="#72836f" opacity=".65">
        <circle cx="45" cy="27" r="13" /><circle cx="93" cy="23" r="10" />
        <circle cx="887" cy="25" r="15" /><circle cx="930" cy="34" r="11" />
      </g>
      <g stroke="#d8dfce" stroke-width="3" opacity=".8">
        <path v-for="x in [58, 196, 334, 472, 610, 748, 886]" :key="x" :d="`M${x} 85v250`" />
        <path d="M58 85h828M58 335h828" />
      </g>
      <g v-for="(vehicle, index) in vehicles" :key="vehicle.x" :transform="`translate(${vehicle.x} ${index % 2 ? 181 : 133}) rotate(-2 49 75)`">
        <rect x="3" y="6" width="93" height="151" rx="22" fill="#18231f" opacity=".38" />
        <rect x="8" y="0" width="84" height="147" rx="22" :fill="vehicle.color" />
        <path d="M22 30q4-14 17-16h22q13 2 17 16l7 31H15z" fill="#526a65" />
        <path d="M17 70h66v43H17z" fill="#243630" opacity=".9" />
        <path d="M23 77h54v28H23z" fill="#8ca39c" opacity=".42" />
        <path d="M8 28h8v20H8zm76 0h8v20h-8zM8 112h8v17H8zm76 0h8v17h-8z" fill="#e7e2c6" />
        <rect x="25" y="137" width="49" height="5" rx="2.5" fill="#f3f1e8" opacity=".8" />
      </g>
      <g fill="#e7eee6" font-family="monospace" font-size="12" letter-spacing="1.5" opacity=".9">
        <text x="22" y="402">PARKLINE · LIVE SITE VIEW</text>
        <text x="838" y="402">CAM 01</text>
      </g>
    </svg>
    <span class="parking-image-live"><i></i> LIVE VIEW</span>
  </div>
</template>
