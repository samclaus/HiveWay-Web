<script lang="ts" module>
    import { cubicOut } from "svelte/easing";
    import { get, writable } from "svelte/store";
    import { type TransitionConfig } from "svelte/transition";
    import { Deferred } from "../../lib/async-util";
    import ConfirmationModal from "./ConfirmationModal.svelte";

    /**
     * Indicates that the user canceled a modal. This could be by clicking
     * the backdrop, pressing escape, or clicking a button that then
     * programmatically canceled the modal.
     */
    export class ModalCanceledError extends Error {

        constructor() {
            super("modal canceled");
        }

    }

    type Modal = [any, any, Deferred<any>];

    const current = writable<Modal | undefined>(undefined);

    let prevActiveElement: unknown;

    function restoreFocus(): void {
        if (prevActiveElement instanceof HTMLElement) {
            prevActiveElement.focus();
            prevActiveElement = undefined;
        }
    }

    export function show<T>(component: any, params?: any): Promise<T> {
        // Cancel the current modal, if any
        cancel();

        // So we can restore focus after the modal is closed
        prevActiveElement = document.activeElement;

        // Now show the new modal and return the deferred result promise
        const d = new Deferred<T>();
        current.set([component, params, d]);
        return d.promise;
    }

    export function fire(component: any, params: any): void {
        show(component, params).catch(() => {});
    }

    export function complete(result?: any): void {
        const modal = get(current);

        if (modal) {
            modal[2].resolve(result);
            current.set(undefined);
            restoreFocus();
        }
    }

    export function cancel(): void {
        const modal = get(current);

        if (modal) {
            modal[2].reject(new ModalCanceledError());
            current.set(undefined);
            restoreFocus();
        }
    }

    function slideScaleFade(el: Node, {
        delay = 0,
        duration = 300,
    } = {}): TransitionConfig {
        return {
            delay,
            duration,
            easing: cubicOut,
            css: (t, u) => `
                opacity: ${t};
                transform: translateY(${50 * u}px) scale(${t});
            `,
        };
    }

    export function confirm(
        title: string,
        body: string,
        confirmLabel: string,
    ): Promise<void> {
        return show(ConfirmationModal, { title, body, confirmLabel });
    }
</script>

<script lang="ts">
    function onBackdropClick(ev: MouseEvent): void {
        ev.stopPropagation();
        cancel();
    }

    function onContainerKeydown(ev: KeyboardEvent): void {
        if (ev.key === "Escape") {
            ev.stopPropagation();
            cancel();
        }
    }

    function clickTarget(ev: FocusEvent): void {
        (ev.target as any).click();
    }

    function handleModalKeydown(ev: KeyboardEvent): void {
        // Ported from Svelte 4 'self' event modifier
        if (ev.currentTarget !== ev.target) {
          return;
        }
        if (ev.key === "Tab" && ev.shiftKey) {
            ev.preventDefault();
        }
    }
</script>

<div
    class="container"
    onkeydown={onContainerKeydown}
    role="none">

    <!-- svelte-ignore a11y_click_events_have_key_events -->
    <div
        class="backdrop"
        class:visible={!!$current}
        onclick={onBackdropClick}
        role="none"
    ></div>

    {#if $current}
        {@const ModalComponent = $current[0]}
        <div class="modal-scroller">
            <!-- svelte-ignore a11y_no_noninteractive_element_interactions -->
            <dialog
                id="hw-modal"
                open
                transition:slideScaleFade
                onkeydown={handleModalKeydown}>
                <ModalComponent {...$current[1]} />
            </dialog>
            <a aria-hidden="true" href="#hw-modal" onfocus={clickTarget}></a>
        </div>
    {/if}

</div>

<style>
    .container {
        position: fixed;
        inset: 0;
        pointer-events: none;
    }

    .backdrop {
        position: absolute;
        inset: 0;

        background-color: black;

        opacity: 0;
        transition: opacity 200ms ease-out;
    }

    .backdrop.visible {
        opacity: .75;
        pointer-events: all;
    }

    .modal-scroller {
        position: absolute;
        inset: 0;
        overflow: auto;
        pointer-events: none;
    }

    dialog {
        display: block;

        margin: 40px auto;

        width: 40rem;
        max-width: 100vw;

        padding: 0 12px;

        background-color: white;
        border: 2px solid #000;
        border-radius: 8px;

        pointer-events: all;
        contain: inline-size layout style paint;

        transform-origin: center;
        isolation: isolate;
    }

    dialog:focus {
        outline: none;
    }
</style>
