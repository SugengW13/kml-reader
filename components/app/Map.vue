<script setup lang="ts">
import type {LatLngExpression} from "leaflet";

const props = defineProps({
  polyline: {
    type: Array as () => LatLngExpression[],
    default: []
  }
})

const center = computed(() => {
  if (props.polyline.length === 0) return {lat: -0.7893, lng: 113.9213}

  const coordinates = props.polyline

  const latitudes = coordinates?.map(c => c.lat)
  const longitudes = coordinates?.map(c => c.lng)

  return {
    lat: (Math.min(...latitudes) + Math.max(...latitudes)) / 2,
    lng: (Math.min(...longitudes) + Math.max(...longitudes)) / 2
  }
})

const zoom = computed(() => {
  if (props.polyline.length === 0) return 5

  const coordinates = props.polyline

  const latitudes = coordinates.map(c => c.lat)
  const longitudes = coordinates.map(c => c.lng)

  const latDiff = Math.max(...latitudes) - Math.min(...latitudes)
  const lngDiff = Math.max(...longitudes) - Math.min(...longitudes)

  const latZoom = Math.ceil(Math.log(360 / latDiff) / Math.LN2)
  const lngZoom = Math.ceil(Math.log(360 / lngDiff) / Math.LN2)

  return Math.min(latZoom, lngZoom)
})
</script>

<template>
  <div class="rounded-xl overflow-hidden border shadow-sm w-full aspect-[4/3]">
    <l-map
      :zoom="zoom"
      :center="center"
      :use-global-leaflet="false"
    >
      <l-tile-layer
        url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png"
        attribution="&amp;copy; <a href=&quot;https://www.openstreetmap.org/&quot;>OpenStreetMap</a> contributors"
        layer-type="base"
        name="OpenStreetMap"
      />

      <l-polyline
        v-if="props.polyline.length > 0"
        :lat-lngs="props.polyline"
        color="green"
      />
    </l-map>
  </div>
</template>

<style scoped>

</style>