<script lang="ts">
    import { autofocus } from '../actions/autofocus';
    import { cancel, complete } from '../widgets/ModalContainer.svelte';

    let {
      title,
      body,
      confirmLabel,
    }: {
      title: string;
      body: string;
      confirmLabel: string;
    } = $props();
</script>
<form onsubmit={e => { e.preventDefault(); complete(); }}>

    <h2>
        {title}
    </h2>

    <p>
        {body}
    </p>

    <div class="form-actions">

        <button type="button" onclick={cancel} use:autofocus>
            Cancel
        </button>

        <button type="submit" class="filled warn">
            {confirmLabel}
        </button>

    </div>

</form>
