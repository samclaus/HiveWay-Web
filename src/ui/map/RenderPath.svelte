<script lang="ts">
    import * as L from "leaflet-lite";
    import { onDestroy } from "svelte";
    import type { PathInfo } from "../../state/project-features";

    let {
      map,
      path,
    }: {
      map: L.Map;
      path: PathInfo;
    } = $props();

    function unpackPathCoords(packed: number[]): L.LatLng[] {
        const coords = new Array<L.LatLng>(
            Math.floor(packed.length / 2),
        );

        for (let i = 0; i < coords.length; ++i) {
            coords[i] = new L.LatLng(packed[i*2], packed[i*2 + 1]);
        }

        return coords;
    }

    const layer = new (path.line ? L.Polyline : L.Polygon)([]);

    map.addLayer(layer);

    $effect(() => layer.setLatLngs(unpackPathCoords(path.coords)));
    $effect(() => layer.setStyle(path.styles));

    onDestroy(() => map.removeLayer(layer));
</script>
