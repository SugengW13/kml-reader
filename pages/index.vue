<script setup lang="ts">
const coordinates = ref<{ lat: number, lng: number, alt?: number }[]>([])

function onChangeFile(event: any) {
  const file = event.target.files[0]

  if (!file) return

  const reader = new FileReader()

  reader.onload = (e) => {
    const xmlString = e.target?.result
    const parser = new DOMParser()
    const xmlDoc = parser.parseFromString(String(xmlString), 'application/xml')

    coordinates.value =
      xmlDoc.getElementsByTagName('coordinates')[0]
        .childNodes[0].nodeValue?.split(' ')
        .map(s => s.split(','))
        .map(s => ({
          lng: Number(s[0]),
          lat: Number(s[1]),
          alt: Number(s[2])
        })).filter(c => c.alt && c.lng && c.alt) ?? []
  }

  reader.readAsText(file)
}
</script>

<template>
  <div class="p-10 space-y-10">
    <p class="text-center font-semibold text-4xl">KML Reader</p>
    <div class="w-[720px] mx-auto space-y-5">
      <app-map class="w-full" :polyline="coordinates"/>

      <div>
        <input type="file" @change="onChangeFile">
      </div>
    </div>
  </div>
</template>

<style scoped>

</style>