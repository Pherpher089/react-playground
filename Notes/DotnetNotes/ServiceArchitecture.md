# Dotnet Service Architecture Notes

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

#

## What is a `DbContext`

`DbContext` is the main EF Core class that represents a session with the database.

Thisk of it as a **Unit of Work** + **Change Tracker** + **Query Gateway**.

It does three major things:

### 1) Tracks Objects

When EF loads entities from the database, it tracks them.

Example:

```C#
var workOrder = db.WorkOrders.First();
WorkOrder.SetTitle("New title");

await db.SaveChangesAsync();
```

EF knows:

- which entity changed
- what properties changed
- what SQL update to generate

#

### 2) Translates LINQ -> SQL

When you write:

```C#
var orders = db.workOrders
  . where(x => x.Status == WorkOrderStatus.Open)
  .ToList();
```

EF translates that to something like:

```SQL
SELECT *
FROM WorkOrders
WHERE Status = 1
```

#

### 3) Persists changes

Where you call:

```C#
await db.SaveChangesAsync();
```
