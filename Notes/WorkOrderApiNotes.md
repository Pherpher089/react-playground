# Work Order API Notes

#

## Domain Model

### What is a domain model?

> A set of classes that represent concepts and enforce business ruels.

Not:

- Database tables
- DTOs
- JSON contracts
- EF models
- API responses

Those are infastructure consers.

#

The domain models is pure business meaning.

#

### Concrete Example

Imagine a bad version of WorkOrder:

```c#
public class WorkOrder
{
  public guid Id { get; set; }
  public string Title { get; set; }
}
```

This is just a data container.

There are no rules.

You can create:

```C#
new WorkOrder { Title = ""};
```

That makes no business sense.

#

### A Real Domain Model

A real domain model enforces invarients:

- Title must not be empty
- Title length must be from 3 - 100
- CreatedAt must be set at creation
- Status must start as open
- You can't move from Done->Open

Now it looks like this:

```C#
public class WorkOrder
{
    public Guid Id { get; }
    public string Title { get; private set; }
    public WorkOrderStatus Status { get; private set; }
    public DateTime CreatedAtUtc { get; }

    public WorkOrder(string title)
    {
        if (string.IsNullOrWhiteSpace(title))
            throw new ArgumentException("Title cannot be empty.");

        Id = Guid.NewGuid();
        Title = title;
        Status = WorkOrderStatus.Open;
        CreatedAtUtc = DateTime.UtcNow;
    }
}
```

Now:

- you can't create an invalid WorkOrder.
- The rules live inside the modle.
- No controller can bypass them.

That's domain modeling

# Q: What is a struct again?

In C#, there are two fundamental catagories of types:

- Reference types -> `class`
- Value types -> `struct`

## Classes (reference typess)

- stored on the heap
- Variables hold references (pointers)
- can be null
- compared by reference by default

```C#
var a = new User(...);
var b = a;

b.FirstName = "Chris";
// a.FirstName is now also "Chris"
```

Because both vaiables are the same object.

#

# Structs (Value types)

- Stored inline (stack or inside another object)
- Variables hold the actual value
- Cannot be null (unless `Nullable<T>`)
- Copied on assignment

```C#
var a = new UserId(Guid.NewGuid());
var b = a;

b = new UserId(Guid.Empty);
// a is unchanged
```

Structs behave like primitievs (`int`, `bool`, `DateTime`).

#

# Why `UserId` is a struct

We used:

```C#
public readonly reacord struct UserId(Guid Value)
```

We choose a struct because:

- It represents a small, immutable value
- It should behave like a Guid
- It shouldn't be nullable by default
- It avoids unnessisary heap allocation

This is called a **Value Object** in domain modeling.

User is not an entity -- it's just a strongly typed wrapper around a Guid.

#

# What does `record` mean?

`record` is a C# keyword introduced in C# 9.

It's used for data-centric types with value equality.

There are two kinds:

- `record class`
- `record struct`

I used a `record struct`

#

## what odes `record`give you?

it automatically gives:

- Value-based equality
- `ToString()`
- Proper `GetHashCode()`
- Deconstruction support

Example:

```C#
var a = new UserId(guid);
var b = new UserId(guid);

Console.WriteLine(a == b); // true
```

If this were a normal struct without overriding equality, it still compares by value -- but records generate cleaner, safer equality automatically.

#

is `record struct` a class? -> No.

#

# What is `nameof` again?

`nameof` is a compile-time operator that return the name of a symbol as a string.

Example:

```C#
throw new argument exception("Title cannot be empty. ", nameof(title));
```

`nameof(title)` becomes:

```C#
"title"
```

But here's why it matteres:

If you rename the parameter `title` -> `workOrderTitle`,
`naemof(title)` automatically updates.

If you had written:

```C#
"Title"
```

That sring would automatically update.

#

## Why this matters in real projects

- Avoids magic strings
- Refactors-safe
- Prevents typeos
- Compiler checks it

#

# Lanugage Level VS Domain Modeling Level

## Language Level (C#)

These are runtime concepts:

- Reference Type -> `class`
- Value Type -> `struct`
- `record` -> adds value-based equality
- `record struct` -> value type + record behavior

These describe memory behavior.

#

## Domain Modeling Level (DDD Concepts)

These are business modeling concepts:

- Entity
- Value Object

These describe meaning and identiy, not memory layout.

#

## What is an Entity?

An **Entity** is something that:

- Has Identity
- Can change over time
- Is tracked by who it is, not what it looks like

In my system:

- `WorkOrder` is an entity.
- `User` is an entity.

Why?

Because:

If two work orders have:

- Same title
- Same Priority
- Same description

They are still NOT the same work order.

Why?

Because they have different IDs.

Identity matters.
