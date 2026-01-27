# Responsibility of Things

`Different kinds of change should not collide in the same place.`

## On Sentence Definition

> A thing should have a clear reason to change

These things can be:

- Finction
- React Component
- A Hook
- A File
- A Service
- An API Endpoint
- A Class

## Why this matters (Big Picture)

Jr. code usually brakes because (wrong):

- Things do too much
- Or do too little but in the wronge place
- Or know about things they shouldn't even be aware of
  Sr. code (right):
- has boring, obvious responsibilities
- Is easy to delete, move or replace
- Changes in one place don't ripple everywhere

## The 4 Responsibility Questions

1. What is this thing responsible for?
2. What is it _not_ responsible for?
3. Who is allowed to talk to is?
4. What would force this thing to change?

If these can not be answered **clearly** the responsibility is wrong.

### Example 1: React Component (Common Mistake)

#### ❌ Too many responsibilities

```tsx
function UserProfile() {
  const [user, setUser] = useState(null)

  useEffect(()=>{
    fetch('/api/user')
    .then(res=>res.json())
    .then(setUser)
  }[])

  const handleSave = async ()=>{
    await fetch('/api/user', {method: 'POST', body: JSON.strigigy(user)})
    aleart('Saved!')
  }

  if(!user) return <Spinner />

  return (
    <div>
      <input value={user.name} onChange={...}>
      <button onClick={handleSave}>Save</button>
    </div>
  )
}
```

#### What responsibilities does this have?

- Fetching data ❌
- Managing server state ❌
- Rendering UI state ❌
- Managing UI state ❌
- Handling side effects ❌

This is 5 responibilities

#

### Example 2: Split by Responsibility (Senior Pattern)

#### ✅Responsibilities clearly separated

`useUserProfile.ts`

```ts
export function useUserProfile() {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetch('/api/user')
      .then((res) => res.json())
      .then((data) => {
        setUser(data);
        setLoading(false);
      });
  }, []);

  const saveUser = async (updateUser) => {
    await fetch('/api/user', {
      method: 'POST',
      body: JSON.stringify(updatedUser),
    });
  };
  return { user, setUser, loading, saveUser };
}
```

#### Responisibility

> Manage user profile data + server interaction

#

`UserProfileView.tsx`

```tsx
export function UserProfileView({ user, onChange, onsave }) {
  return (
    <div>
      <input value={user.name} onChange={onChange} />
      <button onClick={onSave}>Save</button>
    </div>
  );
}
```

#### Responsibility

> Render UI and forward user intent.

#

`UserProfile.tsx`

```tsx
export function UserProfile() {
  const { user, setUser, loading, saveUser } = useUserProfile();

  if (loading) return <Spinner />;

  return (
    <UserProfileView
      user={user}
      onChange={(e) => setUser({ ...user, name: e.target.value })}
      onSave={() => saveUser(user)}
    />
  );
}
```

#### Responsibility:

> Wire data to UI

#

### The "Delete Test"

Ask:

> "If I delete this file, what breaks?"

- If everything breaks -> too much responsibility
- If nothing breaks -> probably too little / wrong place
- If one clear thing breaks 👌 perfect

#

### Responsibility Smells🚨

- "This component just grew over time..."
- "We need a quick fix so I added it here"
- "This hook also does navigation now"
- "This util calls the API"
- "This reudcer triggers a toast"

All of these are responisbility leaks.

#

### Target Mental Model

Think in layers, not files:

```scss
UI (renders, no logic)
↓
Feature Logic (hooks, state, orchestration)
↓
Domain Logic (rules, transformations)
↓
Infastructure (API, storage, browser)
```

Each layer:

- Knows about the layer below
- Never about the layer above

#

# Exercise 1

❌ The “Works But Feels Wrong” Component

Imagine a dashboard page that:

- Loads orders
- Filters them
- Shows errors
- Handles selection
- Performs a bulk action
- Shows notifications

```tsx
function OrdersDashboard() {
  const [orders, setOrders] = useState([]);
  const [filteredOrders, setFilteredOrders] = useState([]);
  const [statusFilter, setStatusFilter] = useState('all');
  const [selectedIds, setSelectedIds] = useState([]);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    setLoading(true);
    fetch('/api/orders')
      .then((res) => res.json())
      .then((data) => {
        setOrders(data);
        setFilteredOrders(data);
        setLoading(false);
      })
      .catch(() => {
        setError('Failed to load orders');
        setLoading(false);
      });
  }, []);

  useEffect(() => {
    if (statusFilter === 'all') {
      setFilteredOrders(orders);
    } else {
      setFilteredOrders(orders.filter((o) => o.status === statusFilter));
    }
  }, [orders, statusFilter]);

  const toggleSelect = (id: string) => {
    setSelectedIds((prev) => (prev.includes(id) ? prev.filter((x) => x !== id) : [...prev, id]));
  };

  const bulkCancel = async () => {
    try {
      await fetch('/api/orders/cancel', {
        method: 'POST',
        body: JSON.stringify({ ids: selectedIds }),
      });
      alert('Orders cancelled');
      setSelectedIds([]);
    } catch {
      alert('Cancel failed');
    }
  };

  if (loading) return <Spinner />;
  if (error) return <ErrorBanner message={error} />;

  return (
    <div>
      <select value={statusFilter} onChange={(e) => setStatusFilter(e.target.value)}>
        <option value="all">All</option>
        <option value="open">Open</option>
        <option value="shipped">Shipped</option>
      </select>

      <ul>
        {filteredOrders.map((order) => (
          <li key={order.id}>
            <input
              type="checkbox"
              checked={selectedIds.includes(order.id)}
              onChange={() => toggleSelect(order.id)}
            />
            {order.name} ({order.status})
          </li>
        ))}
      </ul>

      <button disabled={!selectedIds.length} onClick={bulkCancel}>
        Cancel Selected
      </button>
    </div>
  );
}
```

1. What is OrdersDashboard responsible for?

```md
- This component fetched and filtering data, handles errors related to that process, handles bulk actions as well as renders the dashboard.
```

## What Responsibilities Are Mixed Here?

1. Data fetching (infastructure)

- Calls `/api/orders`
- Handles loading error

2. Server Side Mutation

- Calls `/api/orders/cancel`

3. Domain Logic

- Filtering by order status
- Selecting/deselecting orders

4. UI State

- Which checkboxes are checked
- which filter is active

5. Presentation

- Rendering lst
- Rendering filters
- Rendering buttons
- rendering error/loading stats

6. Side Effects

- aleart() notifications

# Exersise 1.2

1. What should this component be responsible for?
   I think this component should be responsible for composign the individual aspects of this Dashboard and managin the UI state which is passed as props into the individual components

2. What responsibilities clearly do not belong here?
   Data fetching, filtering, rendering UI and throwing errors all donot belong here.

3. What would cause this file to change most often?
   Andy updates to the way the dashboard looked or worked would change this file. For instance, when the stakeholders want a different layout, or a new kind of filter, this component would change.

4. What change would be painful right now?
   I think the most painful kind of change would be using bulk select somewhere else (stollen from your later notes)

#

## Senior Refactoring Direction

Here's how a senior dev would _mentally_ break this up

`useOrders()` Fetch orders, expose loading/error
`useOrderFilter()` Filter logic (pure, testable)
`useBulkSelection()` Selection state + helpers
`useBulkCancelOrders()` Mutation + side effects
`OrdersDashboard` Orchestrate hooks
`OrdersListView` Render UI

Each one: - Has one reason to change - Can be tested or reused independently - Can be deleteted without nuking everything

#

# Mini rule that will save you constantly

When you're unsure if somthing belongs in the dashboard:
"Props are allowed; implementations are not"

- Passing `onCancelSelected` ✅
- Writing `fetch('/api/orders/cancel')`inside the dashboard ❌
- Passing `filter` and `onFilterChange` ✅
- Writing the filtering algorithm in the dashboard ❌ (put in a pure function/hook)

# Exersise 1.3

What is the dashboard **NOT** responsible for

- The dashboard is not responisble for filtering logic
- The dashboard is not responsible for fetching orders data
- The dashboard is not responisble for rendering the data lists
  - _Correction (The dashboard is not responsible for how the data lists are rendered.)_
- The dashboard is not responsible for handling api erros
- The dashboard is not responsible for mutations such as cancleing the orders request

Foot Notes:

## What is a side effect?

    - Anything your code does that effects the world outside it's own return value

Why side effects matter for responsibility
Side Effects:

- Are hard to test
- Are hard to undo
- Tend to break in weird ways
- Ripple acrross the app

This is why we contain them, not scatter them.

A dashboard component should request side effects, not perform them.

### Mental Model to memorize

> UI asks for effects. Hooks perform effects.

## What is a mutation

### Simple definition

A mutation is:

> Any operation that changes existing data.
> In Contrast:

- Query -> reads data
- Mutation -> Changes data

Example (Frontend Context)
Fetch orders Query
Cancel orders Mutation
Update user Name Mutation
Creat new ordre Mutation
Delete an ordre Mutation

### Key Distinction

❌ Mutation = API call
✅ Mutation = state change in the system

The system might be:

- Your backend
- Your frontend cache
- Global app state

# Responsibility Summary

Dashboard

- Owns UI coordination
- Owns shared UI state
- Delegates Queries
- Delegates mutations
- Never performs side effects directly

Hooks/Services

- Performs side effects
- Contains mutations
- Handels error mapping
- Talks to infastructure
