<script lang="ts">
	import { onMount, onDestroy } from 'svelte'
	import { browser } from '$app/environment'

	let mapElement: HTMLElement
	let map: any //: L.Map
	export let latlng: number[]
	let zoom = 10

	onMount(async () => {
		if (browser) {
			const L = await import('leaflet')

			const baseMaps = {
				OpenStreetMap: L.tileLayer('https://{s}.tile.osm.org/{z}/{x}/{y}.png', {
					maxZoom: 19,
					attribution:
						'<span style="font-size: 100%">&copy; <a target="_blank" href="http://osm.org/copyright">OpenStreetMap</a> contributors</span>'
				}),
				OpenTopoMap: L.tileLayer('https://{s}.tile.opentopomap.org/{z}/{x}/{y}.png', {
					maxZoom: 17,
					attribution:
						'<span style="font-size: 85%">Map data: &copy; <a href="http://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors, <a href="http://viewfinderpanoramas.org">SRTM</a> | Map style: &copy; <a href="https://opentopomap.org">OpenTopoMap</a> (<a href="https://creativecommons.org/licenses/by-sa/3.0/">CC-BY-SA</a>)</span>'
				}),
				'Esri WorldImagery': L.tileLayer(
					'https://server.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer/tile/{z}/{y}/{x}',
					{
						attribution:
							'<span style="font-size: 85%">Tiles &copy; Esri &mdash; Source: Esri, i-cubed, USDA, USGS, AEX, GeoEye, Getmapping, Aerogrid, IGN, IGP, UPR-EGP, and the GIS User Community</span>'
					}
				)
			}

			const blueIcon = L.icon({
				iconUrl: '/marker-icon.png',
				iconSize: [25, 41],
				iconAnchor: [12, 41]
			})
			const locationMarker = L.marker(latlng as L.LatLngTuple, {
				icon: blueIcon,
				draggable: false,
				zIndexOffset: 1000
			})

			map = L.map(mapElement).setView(latlng as L.LatLngTuple, zoom)
			L.control.layers(baseMaps).addTo(map)
			baseMaps.OpenStreetMap.addTo(map)
			// map.attributionControl.setPrefix(false)
			L.control.scale({ metric: true, imperial: true, position: 'bottomleft' }).addTo(map)
			locationMarker.addTo(map)
		}
	})

	onDestroy(async () => {
		if (map) {
			map.remove()
		}
	})
</script>

<div
	bind:this={mapElement}
	class="w-full border border-black"
	style="height: 350px; position:relative; z-index:0;"
/>

<style>
	@import 'leaflet/dist/leaflet.css';
</style>
