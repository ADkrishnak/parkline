<script setup>
const usagePoints = [
  { x: 34, y: 121, label: '06:00' },
  { x: 83, y: 96, label: '09:00' },
  { x: 132, y: 108, label: '12:00' },
  { x: 181, y: 69, label: '15:00' },
  { x: 230, y: 85, label: '18:00' },
  { x: 279, y: 54, label: '21:00' },
]

const vehiclePoints = [
  { x: 34, y: 129, label: '06' },
  { x: 83, y: 108, label: '09' },
  { x: 132, y: 113, label: '12' },
  { x: 181, y: 75, label: '15' },
  { x: 230, y: 91, label: '18' },
  { x: 279, y: 48, label: '21' },
]

const vehicleTypes = [
  { name: 'Car', percent: 62.5, count: 20, color: '#548f63' },
  { name: 'Bike', percent: 25, count: 8, color: '#77a9db' },
  { name: 'SUV', percent: 12.5, count: 4, color: '#e9b64d' },
]

function linePath(points) {
  return points.map((point, index) => `${index === 0 ? 'M' : 'L'} ${point.x} ${point.y}`).join(' ')
}

function areaPath(points) {
  return `${linePath(points)} L ${points[points.length - 1].x} 150 L ${points[0].x} 150 Z`
}
</script>

<template>
  <section class="admin-analytics" aria-label="Parking analytics">
    <article class="panel analytics-card">
      <div class="analytics-heading">
        <div><h2>Parking usage</h2><p>Occupancy throughout today</p></div>
        <span class="analytics-period">TODAY</span>
      </div>
      <div class="chart-frame">
        <div class="chart-y-labels" aria-hidden="true"><span>100%</span><span>50%</span><span>0%</span></div>
        <svg class="trend-chart usage-chart" viewBox="0 0 310 176" role="img" aria-labelledby="usage-chart-title usage-chart-desc">
          <title id="usage-chart-title">Parking usage today</title>
          <desc id="usage-chart-desc">Parking occupancy trends upward through the day, reaching 64 percent by 9 PM.</desc>
          <defs>
            <linearGradient id="usage-fill" x1="0" x2="0" y1="0" y2="1">
              <stop offset="0%" stop-color="#57bd70" stop-opacity=".42" />
              <stop offset="100%" stop-color="#57bd70" stop-opacity=".02" />
            </linearGradient>
          </defs>
          <g class="chart-grid-lines">
            <path d="M30 26H300M30 82H300M30 150H300" />
          </g>
          <path class="chart-area" :d="areaPath(usagePoints)" fill="url(#usage-fill)" />
          <path class="chart-line usage-line" :d="linePath(usagePoints)" />
          <circle v-for="point in usagePoints" :key="point.label" class="chart-point usage-point" :cx="point.x" :cy="point.y" r="3" />
          <text v-for="point in usagePoints" :key="`usage-${point.label}`" class="chart-x-label" :x="point.x" y="169" text-anchor="middle">{{ point.label }}</text>
          <text class="chart-value-label" x="279" y="43" text-anchor="middle">64%</text>
        </svg>
      </div>
    </article>

    <article class="panel analytics-card">
      <div class="analytics-heading">
        <div><h2>Vehicle count</h2><p>Vehicles on site today</p></div>
        <span class="analytics-total">32 <small>total</small></span>
      </div>
      <div class="chart-frame">
        <div class="chart-y-labels" aria-hidden="true"><span>40</span><span>20</span><span>0</span></div>
        <svg class="trend-chart count-chart" viewBox="0 0 310 176" role="img" aria-labelledby="count-chart-title count-chart-desc">
          <title id="count-chart-title">Vehicle count today</title>
          <desc id="count-chart-desc">The number of vehicles on site changes during the day and reaches 32 by 9 PM.</desc>
          <g class="chart-grid-lines">
            <path d="M30 26H300M30 82H300M30 150H300" />
          </g>
          <path class="chart-line count-line" :d="linePath(vehiclePoints)" />
          <circle v-for="point in vehiclePoints" :key="point.label" class="chart-point count-point" :cx="point.x" :cy="point.y" r="3" />
          <text v-for="point in vehiclePoints" :key="`count-${point.label}`" class="chart-x-label" :x="point.x" y="169" text-anchor="middle">{{ point.label }}</text>
          <text class="chart-value-label count-value-label" x="279" y="37" text-anchor="middle">32</text>
        </svg>
      </div>
    </article>

    <article class="panel analytics-card distribution-card">
      <div class="analytics-heading">
        <div><h2>Vehicle types</h2><p>Current distribution</p></div>
      </div>
      <div class="donut-content">
        <div class="donut-chart" role="img" aria-label="Vehicle types: 20 cars, 8 bikes, 4 SUVs">
          <svg viewBox="0 0 112 112" aria-hidden="true">
            <circle class="donut-track" cx="56" cy="56" r="42" />
            <circle
              v-for="(type, index) in vehicleTypes"
              :key="type.name"
              class="donut-segment"
              :style="{ color: type.color }"
              cx="56"
              cy="56"
              r="42"
              pathLength="100"
              :stroke-dasharray="`${type.percent} ${100 - type.percent}`"
              :stroke-dashoffset="-vehicleTypes.slice(0, index).reduce((total, item) => total + item.percent, 0)"
            />
          </svg>
          <div class="donut-center"><strong>32</strong><span>Vehicles</span></div>
        </div>
        <ul class="donut-legend">
          <li v-for="type in vehicleTypes" :key="type.name">
            <i :style="{ backgroundColor: type.color }"></i>
            <span>{{ type.name }}</span>
            <b>{{ type.percent }}%</b>
          </li>
        </ul>
      </div>
    </article>
  </section>
</template>
