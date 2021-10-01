<!-- https://single-spa.js.org/docs/api/#routing-event -->
<svelte:window on:single-spa:routing-event={onChangedRoute}/>

{#if props}
    {#await props}
        <p>... Loading...</p>
    {:then prop}
        <slot {prop} />
    {:catch err}
        <p>... {err} ...</p>
    {/await}
{:else}
    <p>... Initializing ...</p>
{/if}

<script>
export let modulePath, update
let props

const onChangedRoute = ({ currentTarget }) => {
    if (!(update && modulePath)) return

    // path: /<module>[/<frag1>[/<frag2>[/...]]]
    const [ , module, ...fragments ] =
        currentTarget.location.pathname.split('/')

    // ignore events of other modules
    if (modulePath !== module) return

    // deliver the update to the slot via props
    props = update(...fragments)
}
</script>
