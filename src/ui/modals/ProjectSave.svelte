<script lang="ts">
    import { createProject, modifyProjectMetadata, type ProjectInfo } from "../../state/projects";
    import { cancel, complete } from '../widgets/ModalContainer.svelte';
    import TextArea from '../widgets/TextArea.svelte';
    import TextField from '../widgets/TextField.svelte';

    let { proj }: { proj?: ProjectInfo | undefined } = $props();

    let name = $state('');
    let desc = $state('');

    if (proj) {
        name = proj.name;
        desc = proj.desc;
    }

    function onsubmit(ev: SubmitEvent): void {
        ev.preventDefault();

        if (proj) {
            modifyProjectMetadata({
                id: proj.id,
                name,
                desc,
            });
        } else {
            createProject({
                name,
                desc,
            });
        }
        complete();
    }
</script>
<form {onsubmit}>

    <h2>
        {#if proj}
            Update {proj.name}
        {:else}
            Create a new project
        {/if}
    </h2>

    <div class="form-fields">

        <TextField
            label="Name"
            bind:value={name}
            required
            maxlength={100}
            placeholder="e.g. Fall 2023 w/ Detours"
            autofocus />

        <TextArea
            label="Description"
            hint="What is this project for?"
            placeholder=""
            bind:value={desc} />

    </div>

    <div class="form-actions">

        <button type="button" onclick={cancel}>
            Cancel
        </button>

        <button type="submit" class="filled">
            {proj ? "Update" : "Create"} Project
        </button>

    </div>

</form>
