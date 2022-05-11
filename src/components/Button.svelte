<script>
    export let session;
    export let calculateDelta;
    export let current;
</script>

<style>
    button {
        background: none;
        color: white;
        font-family: inherit;
        cursor: pointer;
        font-weight: bold;
        font-size: 1.25rem;
        margin: 20px 20px 0 20px;
        border: none;
    }

    .selected {
        color: red;
    }
</style>

<button
    class={current === session ? 'selected' : ''}
    on:click={() => current = session}
    on:click={() => calculateDelta(session)}>
    {#if session === 'FirstPractice'}
        FP1
    {:else if session === 'SecondPractice'}
        FP2
    {:else if session === 'ThirdPractice'}
        FP3
    {:else}
        {session}
    {/if}
</button>