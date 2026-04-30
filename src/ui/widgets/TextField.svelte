
<label for={inputID}>

    {label}

    {#if !required}
        <span class="faded">(Optional)</span>
    {/if}

    <input
        id={inputID}
        {type}
        {required}
        {maxlength}
        {placeholder}
        value={value || ""}
        bind:this={inputEl}
        oninput={onInput}>

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
      type = "text",
      label,
      hint = undefined,
      value = $bindable(),
      required = false,
      maxlength = undefined,
      placeholder = undefined,
      autofocus = false,
    }: {
      type?: "text" | "email" | "password" | undefined;
      label: string;
      hint?: string | undefined;
      value: string | undefined;
      required?: boolean | undefined;
      maxlength?: number | undefined;
      placeholder?: string | undefined;
      autofocus?: boolean | undefined;
    } = $props();

    const inputID = `hw-textfield-${counter++}`;

    let inputEl: HTMLInputElement;

    onMount(() => {
        if (autofocus) {
            inputEl.focus();
        }
    });

    function onInput(): void {
        value = inputEl.value;
    }
</script>
