{#if props}
    {#each props as prop (prop[idName])}
    <slot {prop} />
    {/each}
{:else}
    <p>... Loading ...</p>
{/if}

<script>
import { onMount } from 'svelte'

export let modulePath, update, idName='id'
let props

// see https://single-spa.js.org/docs/api/#routing-event
const SPA_EVENT = 'single-spa:routing-event'

const onChangedRoute = async ({ currentTarget }) => {
    // path: /<module>[/<frag1>[/<frag2>[/...]]]
    const [ , module, ...fragments ] =
        currentTarget.location.pathname.split('/')

    // Ignore events of other modules
    if (modulePath !== module) return

    // deliver the update to the slot via props
    try {
        // must be awaited since we don't know if it's sync or async
        const newProps = await update(fragments)
        // the directive {#each ...} needs an array
        if (newProps) props = [ newProps ]
    } catch {
        props = false
    }
}

modulePath && update && idName &&
    onMount( () => (
        window.addEventListener(SPA_EVENT, onChangedRoute),
        // to be called on unmounting
        () => window.removeEventListener(SPA_EVENT, onChangedRoute)
    ))
</script>
