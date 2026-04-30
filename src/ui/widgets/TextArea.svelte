
<label for={inputID}>

    {label}

    {#if !required}
        <span class="faded">(Optional)</span>
    {/if}

    <textarea
        id={inputID}
        {required}
        {maxlength}
        {placeholder}
        bind:this={inputEl}
        bind:value></textarea>

    <div class="input-helpers">
        <div class="input-hint">{hint || ""}</div>
        <div>
            {(value?.length || 0) + (maxlength ? "/" + maxlength : "")}
        </div>
    </div>

</label>

<script lang="ts" module>
    let counter = 0;
</script>

<script lang="ts">
    import { onMount } from "svelte";

    let {
      label,
      hint,
      value = $bindable(),
      required = false,
      maxlength,
      placeholder,
      autofocus = false,
    }: {
      label: string;
      hint?: string | undefined;
      value: string | undefined;
      required?: boolean | undefined;
      maxlength?: number | undefined;
      placeholder?: string | undefined;
      autofocus?: boolean | undefined;
    } = $props();

    const inputID = `hw-textarea-${counter++}`;

    let inputEl: HTMLTextAreaElement;

    onMount(() => {
        if (autofocus) {
            inputEl.focus();
        }
    });
</script>
