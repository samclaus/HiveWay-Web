
<label for={selectID}>

    {label}

    {#if !required}
        <span class="faded">(Optional)</span>
    {/if}

    <div class="select-wrapper">
        <select
            id={selectID}
            {required}
            bind:this={selectEl}
            bind:value>
            {@render children()}
        </select>
    </div>

    <div class="input-helpers">
        <div class="input-hint">{hint || ""}</div>
    </div>

</label>

<script lang="ts" module>
    let counter = 0;
</script>

<script lang="ts">
    import { onMount, type Snippet } from "svelte";

    let {
      label,
      hint,
      value = $bindable(),
      required = false,
      autofocus = false,
      children,
    }: {
      label: string;
      hint?: string | undefined;
      value: any;
      required?: boolean | undefined;
      autofocus?: boolean | undefined;
      children: Snippet;
    } = $props();

    const selectID = `hw-select-${counter++}`;

    let selectEl: HTMLSelectElement;

    onMount(() => {
        if (autofocus) {
            selectEl.focus();
        }
    });
</script>
