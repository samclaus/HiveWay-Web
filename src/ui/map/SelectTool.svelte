<script lang="ts">
    import * as L from "leaflet-lite";
    import { onDestroy } from "svelte";
    import { PROJECT_FEATURES, deleteCircle, deletePath, deleteStop } from "../../state/project-features";
    import Icon from "../widgets/Icon.svelte";
    import IconButton from "../widgets/IconButton.svelte";

    let { map }: { map: L.Map; } = $props();

    type FeatureType = "Stop" | "Polyline" | "Polygon" | "Circle";
    type FeatureModel = [FeatureType, string, string];

    let features: FeatureModel[] = $derived.by(() => {
      // TODO: reuse existing model array and make sure that clearing it doesn't cause
      // Svelte to do extra work
      const f = $PROJECT_FEATURES, models: FeatureModel[] = [];

      for (const s of f.stops) {
          models.push(["Stop", s.id, s.name]);
      }
      for (const p of [...f.paths].sort((l, r) => (+l.line) - (+r.line))) {
          models.push([
              p.line ? "Polyline" : "Polygon",
              p.id,
              p.name,
          ]);
      }
      for (const c of f.circles) {
          models.push(["Circle", c.id, c.name]);
      }

      return models;
    });

    function onMapClick({ latlng }: any): void {
        // TODO
    }

    function onDeleteClick(type: FeatureType, id: string): void {
        // TODO: error handling
        if (type === "Stop") {
            deleteStop(id);
        } else if (type === "Polyline" || type === "Polygon") {
            deletePath(id);
        } else if (type === "Circle") {
            deleteCircle(id);
        }
    }

    map.on("click", onMapClick);
    // TODO

    onDestroy((): void => {
        // TODO
        map.off("click", onMapClick);
    });
</script>

{#if !features.length}
<div class="placeholder">
    <Icon
        name="cursor"
        color="primary"
        size={72} />
    <h3>Nothing to Select</h3>
    <p>
        This project does not contain any map features yet. Try using one
        of the tools above to create some stops or geometry on the map.
    </p>
</div>
{:else}
<div class="padding">
    <p>
        Click a stop, polygon, or other feature on the map to view and
        edit it. You can select a set of features by holding <kbd>⌘</kbd>
        or <kbd>Ctrl</kbd> while you click and drag the mouse on the map.
        Or you can select features below.
    </p>
    <ul>
        {#each features as [type, id, name] (id)}
            <li class="card">
                <h3>{name}</h3>
                <h4>{type}</h4>
                <div class="card-actions">
                    <IconButton
                        label="Delete"
                        icon="delete"
                        color="warn"
                        onclick={() => onDeleteClick(type, id)} />
                </div>
            </li>
        {/each}
    </ul>
</div>
{/if}

<style>
    .padding {
        padding: 0 48px;
    }

    ul {
        padding: 0;
    }

    li {
        margin: 24px 0;
    }
</style>
