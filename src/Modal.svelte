<script>
    import {onMount} from "svelte";

    let modalTarget;
    let modalElement;
    let open = function () {};
    let close = function () {};
    export let isOpen = false;

    $: { if (isOpen)
        open()
    else close() }

    onMount(() => {
        modalTarget = new bootstrap.Modal(modalElement, {backdrop: 'static', keyboard: true, focus: true});
        open = () => {
            modalTarget.show();
        };
        close = () => {
            modalTarget.hide();
            isOpen = false;
        }

    } )
</script>
<slot name="buttonToOpen">

</slot>
<div class="modal fade" tabindex="-1" id="modal" bind:this={modalElement}>
    <div class="modal-dialog">
        <div class="modal-content">
            <div class="modal-header">
                <h5 class="modal-title">
                    <slot name="title">
                    </slot>
                </h5>
                <button type="button" class="btn-close" aria-label="Close" on:click={close}></button>
            </div>
            <div class="modal-body">
                <slot name="body">
                </slot>
            </div>
            <div class="modal-footer">
                <slot name="footer">
                </slot>
            </div>
        </div>
    </div>
</div>