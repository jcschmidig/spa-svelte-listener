# spa-svelte-listener
Listening to single-spa events in svelte

Goal
----
This component is a simple wrapper listening to the single-spa events of
the calling module while delivering the updates to the component(s)
in the slot.

Usage in svelte
---------------
```
<script>
import Listener from './SpaListener.svelte'
import Form     from './Form.svelte'        // tbd

const modulePath = 'form'
const update = (schemaId, recId) =>
    getSchema(schemaId).then( schema => ({ schema, recId }) )
//
const getSchema = async schemaId => {   // tbd
    await ...
}
</script>

<Listener
    {modulePath}
    {update}
    let:prop={{schema, recId}}
>
    <Form {schema} {recId} />
</Listener>
```

Remarks
-------
- the `modulePath` property ensures that only events for the given path
  are being processed.
- the `update` function should return an object containing the information
  needed. The function may be executed sync or async.
- the `update` function may take as many arguments as there are fragments
  in the current path delivered by the single-spa event.
- the `let:prop` directive may expose the properties needed in the slot.
  They must be taken from the object returned by the update function.
