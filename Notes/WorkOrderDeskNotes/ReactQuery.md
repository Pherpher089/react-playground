# React Query

## Core idea

React Query is a server state manager

NOT:

- UI State (linke form inputs)
- Not global state like Redux

It managers:

> "Data that comes in from the server"

#

### Two primitives

1. `useQuery` -> GET data

```ts
useQuery({
  queryKey: ['workOrders'],
  queryFn: listWorkOrders,
});
```

#### What this does

- runs the function
- caches results
- gives you:
  - data
  - loading
  - error

Think:

> "Fetch and cache this data"

#

### 2. `useMutation` -> change data

```ts
useMutation({
  mutationFn: createWorkOrder,
});
```

#### What this does

- runs when YOU trigger it
- not automatic
- use for:
  - POST
  - PUT
  - DELETE

Think

> "Perform an action"

#

## The missing connection: Cache

this is the part that makes React Query powerful.

#

Example:

You create a work order:

```ts
mutation.mutate(input);
```

but your list won't update automatically unless you tell React Query:

```ts
queryClient.invalidateQueries(['workOrders']);
```

That means:

> "Hay, the data for this key is now stale -> refetch it"
