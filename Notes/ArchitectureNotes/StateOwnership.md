# State Ownership

## Core Question:

> Who is allowed to own this state?

Ownership answers:

- Who can change it?
- Who can read it?
- Who is affected when it changes?
- What breaks if it moves?

## Single most important rule:

> State should live at the lowest level that needs to coordinate it.

Not highest.
Not "where it's convenient."
Not "Where I first needed it."

## What "owning state" actually means

Owning state is not just:

```ts
const [x, setX] = useState();
```

Ownership means:

- You decide when it changes
- You decide what it means
- You decided who is notified
- You deal with **invalid states**

If a component _can't_ answer those questions confidently, it shouldn't own the state.

#

## The 4 Catagories of State

1. UI State

> "What is the user doing _right now_?"

Examples:

- is dropdown open?
- selected tab
- checkbox state
- input text
- modal visibility

Ownership:
👉 The component rendering the UI (or it's immediate parent)

Smell if wrong:
You lift it "just in case" and everything rerenders

#

2. Shared UI State

> "Multiple UI Pieces must agree"

Examples:

- selected rows in a table
- active filter
- current step in a wizard

Ownership:

👉 The _closest common parent_ that coordinates those pieces

this is where dashboards ofthen live.

#

3. Serve / Remote State

> "Data that comes from or is persisted somewhere else."

Examples:

- fetched orders
- user profile
- inventory
- save data in falling star 😉

Ownership:

👉 A data layer (query hook, service, cache)

Not:

- individual components
- UI layers

Key Insight:

This state already has an owner - _the server_. You are just syncing it.

#

4. Domain Satate

> "What dose this data _mean_?"

Examples:

- Wheather an order can be canceled
- how filtering works
- weather ths ship is "dead" vs "disabled"

Ownership:
👉 Pure logic (functions, reducers, domain hooks)

This state should:

- be testable
- not depend on react
- not know about UI

#

## Why people get stuck (very common trap)

People try to asnwer:

> "Where should state live?"

But the better question is:

> "Who suffers if the state changes?"

That tells you the owner.

#

## Concrete example

imagine this state:

```ts
const [selectedOrderIds, setSelectedOrderIds] = useState<string[]>([]);
```

Ask:

- Does the server care?❌
- Does filtering logic care?❌
- Do multiple UI elements care?✅
- Does one component coordinate bahavior based on it?✅

👉 Therefore:

Shared UI State -> owned by the dashboard

Not the list item
Not the checkbox
Not the API layer

#

## Falling Start tie-in

Think about

- Ship health
- Scrap Invenotory
- Station Integrity
  Ask:
- Is this UI state? (no)
- Is this server state? (Maybe Later)
- Is this domain state? (Yes)

This is why game code feels different when done right - State ownership is clearer.

## Ownership vs Storage

Owner -> Source of truth
Storage -> Where you cache/sync it

For `orders[]`

- Owner: The backend
- Storage: Redux/React Query/local state/cache

When you put the server data into Redux:

- You are mirroring server state
- Not redefining ownership

#

## Where Redux fits

Redux can store:

- UI State ❌ (Usually a smell)
- Domain state⚠️ (Sometimes)
- Server State ⚠️⚠️ (historically common, now often avoided)

  Moder guidance:

- Server state -> React Query / TanStack Query
- Client coordination state -> Redux (if needed)

  But conceptually:

> orders[] is still server state, even if redux holds a copy
> Redux dosent cahnge that _catagory_, only the _mechanism_

## What domain state REALLY is (plane English)

Domain state is:

> Rules and meaning that stay true reguardless of UI, fraomwork, or storage.

Ask:

- Would this logic exist if the app had no UI?
- Would this logic exist on the backend too?

if yes -> Domain.

#

`canCancelOrders(...)` breakdown

this function:

- express a buisness rule
- combines multiple inputs
- returns a truth about the system

It does NOT:

- fetch data
- render UI
- know about react
- cause side effects

#

## Important clerification

`canCancelOrders(...)` is not really "state" in the React sense.

it is:

- derived domain logic
- sometimes call a _selector_ or _policy_

#

## Think of domain logic like physics rules

In falling start terms:

- Ship is destroyed if hull <= 0
- You can cant jump while docked
- Scrap is lost on ship death

Those are not UI decisions.
They are not server decisions.
They're truths of the world

That's domain

#

## Where domain logic should live

Domain logic should:

- be pure
- Be framework-agnostic
- Live in:
  - plain functions
  - reducers
  - domain hooks
  - services without side effects

it should be:

- Easy to test
- Reusable
- boring

If domain logic ends up in components, they get heavy fast.

#

## Clerifying Lens

Ask this for any piece of logic

> If I had to rewrite this app using Unity/CLI/mobile, would this logic still exist?

Yes > Domain
No > UI or infastructre
