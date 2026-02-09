# Form State Ownership

## The rule for today

> State lives where decisions about it are made.
> ("Decisions" = validation, submit enable/disable, transformations, side effects, and what happens next.)

#

## Example Form "Create Work Order"

Requirements:

- Fileds: `title`, `priority`, `assignee`, `dueDate`, `notes`
- Live validation:
  - title required, 5-80 chars
  - priority must be 1-5
  - dueDate can't be in the past
- UX:
  - disable Submit until valid
  - show field errors after "touched"
  - show server error banner on submit failure
  - on success: show toast + navigate to detail page
- bonus
  - if `assignee` changes, we auto-suggest a default priority (business rule)

### Step 1: List the state

UI state

- `isSubmitting`
- `touchedFileds` (which inputs have been blurred)
- `isDirty` (anything changed from initial values)
- `showConfirmLeaveModal`

Form data state

- `values` (title, priority, assignee, dueDate, notes)

Domain/derived state (computed)

- `fieldErrors` (based on rules)
- `isValid` (serived from error)
- `canSubmit` (isValid && isDirty && !isSubmitting)

Server/remote state

- `assigneeOptions[]` fetched from api
- `submitError` from API response
- the created work order returned from the server

Side Effects

- `POST /work-orders`
- toast
- navigation

### Step 2: Ownership decisions (the "lowest coordinator" rule)

#### A) Values: `values`

pwner: the form component (or a dedicated form hook used by it)

why:

- inputs need to read/write them
- submit logic needs them
- validation needs them

this is _shared UI state_ inside the form boundary.

✅ Owned by: CreateWorkOrderForm (or `useCreateWorkOrderForm()`)

#

#### B) `touchedFields`, `isDirty`

Owner: form component (or form hook)

why:

- affects error display policy and "leave page" warning
- purely a UX concern

#

#### C) Validation rules -> `fieldErrors`, `isValid`

this is the subtle one.

Owner: Domain Logic (pure functions), _called by_ the form hook/component.

Why:

- "title required" and "dueDate not past" are rules that should be testable without React
- the form decides when the show errors (touched), but the rules should be seperated

✅ Owned by: `validateWorkOrder(values)` (pure)
Form _uses_ it to compute erros

important: errors can be computed, not stored:

- Prefer **derived** over stored state when possible

#

#### D) `assigneeOptions[]`

Owner: server state layer (query hook)

Why:

- it comes from the API
- needs cachinig/retry/loading behavior
- multiple forms/screens might reuse it

✅ Owned by: `useAssigneesQuery()`

The form consumes it.

#

#### E) Submit: `isSubmitting`, `submitError`, POST call

Owner: mutation layer (mutation hook)

why:

- submitting is a side-effect
- error mapping belongs near the call
- avoids duplicating submit behavior across screens

✅ Owned by: `useCreateWorkOrderMutation()`

Form triggers it:

- mutate(values)

#

#### F) Toast + navigate on success

Two good patterns:

Pattern 1 (common, fine): keep side effects in the form

- form calls mutation
- mutation returns success
- from decides UI follow-ui (toast/navigate)

Owner of side-effects: form orchestration layer
(Still ok because it's "screen behavoir," not buiness ruels.)

Pattern 2 (also valid): mutation hook accepts callbacks

- `useCreateWorkOrderMutation({ onSuccess })`

Either is acceptable: the key is **don't bury navigatoin inside low-level API utilities.**

#

###
