# spa-svelte-listener
Listening to single-spa events in svelte

Motivation
----------
This component covers a problem when using single-spa with Svelte.
Within the window EventListener, asynchronous updates don't play well
with the `{#await}` directive. Therefore it uses `{#each}` instead.
And it hides some nasty technical details.

Goal
----
This component is a simple wrapper listening to the singls-spa events of
the calling module while delivering the updates to the component(s)
in the slot.

Usage in svelte
---------------
```
<script>
import Router from './Router.svelte'    // this component
import Form from './Form.svelte'        // tbd
const modulePath = 'form'
const update = async ([ schemaId, recId ]) => schemaId &&
    getSchema(schemaId)
        .then( schema => ({ schema, recId , id: (recId || schemaId) }))
        .catch( _ => false )
//
const getSchema = async schemaId => {   // tbd
    ...
}
</script>

<Router
    {modulePath}
    {update}
    let:prop={{schema, recId}}
>
    <Form {schema} {recId} />
</Router>
```

Remarks
-------
- the `modulePath` property ensures that only events for the given path
  are executed.
- the `update` function should return an object containing the information
  needed. Returning a falsy value keeps the informations as is.
  The function may be executed sync or async.
- the `update` function may take one argument which is an array of the
  fragments of the current path delivered by the single-spa event.
- the `idName` property should give the name of the object's property to
  be used as identification. If omitted, there must be property in then
  object named `id` to be used instead.
- the `let:prop` directive may expose the properties needed in the slot.
  They must be taken from the object returned by the update function.
