# Domains in a Nutshell

```mathmatica
UI / Presentation
↓
Application / Feature logic
↓
Domain logic
↓
Infrastructure
```

## UI / Presentation Layer

What it cares about

- rendering
- layout
- interaction
- visual state
  What would cause it to change
- design updates
- UX Tweeks
- accessibility improvements
  What it should NOT care about
- How data is fetched
- What "cancel" means
- API endpoints
  Think:
  > "If I swap the backend, this should't change"

#

## Application/Feature Layer (this is a tricky one)

This is where the dashboard, flows, and screens live.

What it cares about

- coordinating things
- orchestrating steps
- gluing pieces together
- deciding _when_ something happens

What would cause it to change

- workflow changes
- feature behvaior changes
- new interactions

What it should NOT care about

- low-level API details
- raw UI rendering

The `OrdersDashboard` lives here.

This layer feels fuzzy because it's about coordination, not rules or visuals.

#

## Domain Layer (rules & meaning)

This is where people get lost because most frontend codebaceses _don't name it explicity_

What it cares about

- business rules
- invariants
- transformations
- "what does this mean?"

What would cause it to change?

- what counts as "open" vs "closed"
- weather an order can be canceled
- how filtering should work

What would cause it to change

- product decision
- policy changes
- rule changes

what is should NOT care about

- react
- fetch
- UI
- browser APIs

This layer is often:

- pure functions
- utility modules
- logic heavy hooks

## Infastructure Layer

What it cares about

- Talking to the outside world

Examples

- `fetch`
- `localStorage`
- timers
- auth tokens
- APIs

What would cause it to change

- tech swaps
- environment changes
- platform differences

This is where side effects live.
