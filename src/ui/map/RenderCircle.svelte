<script lang="ts">
    import * as L from "leaflet-lite";
    import { onDestroy } from "svelte";
    import type { CircleInfo } from "../../state/project-features";

    let {
      map,
      circle,
    }: {
      map: L.Map;
      circle: CircleInfo;
    } = $props();

    const layer = new L.Circle(new L.LatLng(...circle.center), circle.styles);
    map.addLayer(layer);

    $effect(() => layer.setLatLng(new L.LatLng(...circle.center)));
    $effect(() => layer.setRadius(circle.radius_meters));
    $effect(() => layer.setStyle(circle.styles));

    onDestroy(() => map.removeLayer(layer));
</script>
