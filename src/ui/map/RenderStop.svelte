<script lang="ts">
    import * as L from "leaflet-lite";
    import { onDestroy } from "svelte";
    import type { StopInfo } from "../../state/project-features";

    let {
      map,
      stop,
    }: {
      map: L.Map;
      stop: StopInfo;
    } = $props();

    const marker = new L.Circle(new L.LatLng(stop.lat, stop.lng), {
        color: "#000",
        weight: 1,
        radius: 5,
    });
    map.addLayer(marker);

    $effect(() => marker.setLatLng(new L.LatLng(stop.lat, stop.lng)));

    onDestroy(() => map.removeLayer(marker));
</script>
