<script lang="ts">
    import * as L from "leaflet-lite";
    import { onDestroy } from "svelte";
    import { createCircle } from "../../state/project-features";
    import Icon from "../widgets/Icon.svelte";
    import GeometryFields from "./GeometryFields.svelte";
    import { defaultGeometryStyles } from "./core";

    let { map }: { map: L.Map; } = $props();

    let name = $state("");
    let styles = $state(defaultGeometryStyles());

    const circle = new L.Circle(new L.LatLng(0, 0), {
        ...styles,
        interactive: false,
    });

    let center: L.LatLng | undefined = $state();
    let radiusMeters = $state(100);

    $effect(() => circle.setRadius(radiusMeters));
    $effect(() => circle.setStyle(styles));

    function onMapClick({ latlng }: any): void {
        center = latlng as L.LatLng;
        circle.setLatLng(center);
        map.addLayer(circle);
    }

    map.on("click", onMapClick);

    onDestroy((): void => {
        map.removeLayer(circle);
        map.off("click", onMapClick);
    });

    function cancel(): void {
        map.removeLayer(circle);
        center = undefined;
    }

    function onsubmit(ev: SubmitEvent): void {
        ev.preventDefault();

        if (!center) {
            return;
        }

        // TODO: async handling w/ task system
        createCircle({
            center: [center.lat, center.lng],
            radius_meters: radiusMeters,
            name,
            styles,
        }).then(
            cancel,
            console.error,
        );
    }
</script>

{#if center}
    <div class="toolbar sticky">
        <h2>Creating Circle</h2>
    </div>
    <form {onsubmit}>
        <div class="form-fields">

            <label>
                Radius (Meters)
                <input type="number" bind:value={radiusMeters} step="1">
            </label>

            <GeometryFields bind:name bind:styles />

        </div>
        <div class="form-actions">
            <button type="button" onclick={cancel}>
                Cancel
            </button>
            <button type="submit" class="filled">
                Create Circle
            </button>
        </div>
    </form>
{:else}
    <div class="placeholder">
        <Icon
            name="circle"
            color="primary"
            size={72} />
        <h3>Circle</h3>
        <p>
            Click on the map to determine an initial center for the circle.
            From there, you can edit the radius.
        </p>
    </div>
{/if}
