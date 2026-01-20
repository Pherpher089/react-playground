# Responsibility of Things

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

1. What is its responsibility?

2. List everything else it currently does.

3. Which responsibility does not belong there?
