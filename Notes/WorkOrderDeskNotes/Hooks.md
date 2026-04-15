# Hooks Explained

`useWorkOrders`

```ts
useQuery({
  queryKey: ['workOrders'],
  queryFn: listWorkOrders,
});
```

👉 "Give me all work orders and keep them fresh"

#

`useCreateWorkOrder`

```ts
useMutation({
  mutationFn: createWorkOrder,
  onSuccess: () => {
    querClient.invalidateQueries(['workOrders']);
  },
});
```

👉 "Create something -> then refresh the list"

#

`useWorkOrderDetails`

```ts
useQuery({
  queryKey: ['workOrder', id],
  queryFn: () => getWorkOrderById(id),
  enabled: !!id,
});
```

👉 “Only fetch when an id exists”

#

### Why this is better than a raw fetch

Without React Query:

- you will manually store state
- manually refetch
- manually sync

With React Query:

- Cache is centralized
- UI auto-updates
- fewer bugs

#
