# Part 1 - The Layers (Backend + Frontend)

Think of the app as pipes and responsibilities.

#

## Backend Architecture (.NET app)

### 1. Domain Layer (Core Rules)

> What is allowed in this system

I built:

- WorkOrder
- User
- enums like `Status`, `Priority`

#### Responsibilities:

- enforce rules(status transitions, required fields)
- protect invarients
- NO database, NO HTTP, No Frameworks

Example

```C#
workOrder.StartProgress(userId); // Enforces "must have assignee"
```

### 2. Application Layer (Use Cases)

> "What can the system do?"

I built:

- `CreateWorkOrderHandler`
- `ListWorkOrdersHandler`
- `GetWorkOrderHandler`
- `DeleteWorkOrderHandler`

#### Resoponsibilities:

- orchestrate domain + persistance
- represents real user actions

Example:

```C#
handler.HanldeAsync(command)
```

This:

- creates domain object
- saves it via repository
- returns the results

#

### 3. Infrastructure Layer (Persistance)

> "How do we store/retrieve data?"

I built:

- EF Core `DBContext`
- `WorkOrderRepository`

Important:

```C#
Application -> depends on interface
Infastructure -> implements it
```

This is **Dependency Inversion** (big senior concept).

#

### 4. API layer (Transport)

> "How does the outside world talk to us?"

I built:

- Minimal APIs
- Middleware
- DTOs

Responsibilities:

- map HTTP -> Application
- Serialize/deseriealize JSON
- return responses

Example:

```C#
app.MapPost("/work-orders", ...)
```

#

## Backend Flow (end-to-end)

```
HTTP Request
    ⬇️
API Layer (Map Post)
    ⬇️
Application (Handler)
    ⬇️
Domain (rules enforced)
    ⬇️
Infrastructure (EF Saves)
    ⬇️
Response Returned
```

## Frontend Architecture (React App)

### 1. UI Layer

> "What does the user see?"

I built:

- Forms
- Lists
- Buttons

Pure rendering + user interaction

#

### 2. Hooks Layer (The logic layer)

> "What does this screen DO?"

I bulit:

- `userWorkOrders`
- `useCreateWorkOrders`
- `useDeleteWorkOrder`
- (now) `useUpdateWorkOrder`

Responsibilities:

- Call APIs
- Manager loading/error states
- trigger updates

This is the application layer(frontend equivalent)

#

### 3. API layer

> "How do we talk to the backend?"

I built:

- `createWorkOrder.ts`
- `listWorkOrders.ts`
- `udpateWorkOrders.ts`

Responsibilities:

- fetch()
- handle JSON
- normalize errors

#

4. Domain Types
   > "What is our data?"

I built:

- `WOrkOrderDetails`
- `WorkOrderListItem`

#

## Frontend Flow

```
User clicks button
    ⬇️
UI Calls Hook
    ⬇️
API calls backend
    ⬇️
React Query Updates Cache
    ⬇️
UI re-renders automatically
```
