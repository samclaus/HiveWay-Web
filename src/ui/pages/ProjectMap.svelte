<script lang="ts">
    import * as L from "leaflet-lite";
    import "leaflet-lite/styles";
    import { onDestroy } from "svelte";
    import { PROJECTS } from "../../state/projects";
    import { BREADCRUMBS } from "../Breadcrumbs.svelte";
    import { MAP_TOOLS, ProjectMap, type MapTool } from "../map";
    import IconButton from "../widgets/IconButton.svelte";

    let { params }: {
      params: {
        id: string;
      };
    } = $props();

    let map: L.Map | undefined = $state();
    let tool: MapTool = $state("select");

    $effect(() => {
        const
            id = params.id,
            name = $PROJECTS && PROJECTS.get(id)?.name || id;

        $BREADCRUMBS = [
            ["Projects", "/projects"],
            [name, `/projects/${id}`],
            ["Map", `/projects/${id}/map`],
        ];
    });

    onDestroy((): void => {
        map?.dispose();
    });
</script>

<main>
    <ProjectMap
        projID={params.id}
        onload={m => map = m} />

    <div class="map-tools">
        <div class="toolbar secondary" role="toolbar">
            <IconButton
                label="Select"
                icon="cursor"
                color={tool === "select" ? "primary" : undefined}
                onclick={() => tool = "select"}
                pressed={tool === "select"} />
            <IconButton
                label="Create stop"
                icon="bus-stop"
                color={tool === "add-stop" ? "primary" : undefined}
                onclick={() => tool = "add-stop"}
                pressed={tool === "add-stop"} />
            <IconButton
                label="Polyline"
                icon="polyline"
                color={tool === "polyline" ? "primary" : undefined}
                onclick={() => tool = "polyline"}
                pressed={tool === "polyline"} />
            <IconButton
                label="Polygon"
                icon="polygon"
                color={tool === "polygon" ? "primary" : undefined}
                onclick={() => tool = "polygon"}
                pressed={tool === "polygon"} />
            <IconButton
                label="Circle"
                icon="circle"
                color={tool === "circle" ? "primary" : undefined}
                onclick={() => tool = "circle"}
                pressed={tool === "circle"} />
        </div>

        <!--
            TODO: should be able to select individual point fields in the form,
            and that becomes which point you are re-selecting on the map.
        -->
        {#if map}
            {@const ToolsComponent = MAP_TOOLS[tool]}
            <ToolsComponent {map} />
        {/if}
    </div>
</main>

<style>
    main {
        display: flex;
    }

    main :global(.project-map) {
        height: 100%;
        flex-grow: 1;
    }

    .map-tools {
        height: 100%;
        width: 480px;
        border-left: 1px solid #777;
        background-color: #fff;
        contain: strict;
        overflow-y: auto;
    }
</style>
